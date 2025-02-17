---
title: Flurl使用Polly实现重试Policy
slug: flurl-uses-policy-to-implement-retry-policy
description: 在使用Flurl作为HttpClient向Server请求时，由于网络或者其它一些原因导致请求会有失败的情况
date: 2021-03-15 11:57:53
copyright: Reprinted
author: 非法关键字
originalTitle: Flurl使用Polly实现重试Policy
originalLink: https://www.cnblogs.com/linxmouse/p/14533151.html
draft: False
cover: https://img1.dotnet9.com/2021/03/cover_03.jpg
categories: 
    - .NET
tags: 
    - C#
    - Flurl
    - Policy
---

> 在使用 Flurl 作为 HttpClient 向 Server 请求时，由于网络或者其它一些原因导致请求会有失败的情况，比如`HttpStatusCode.NotFound`、`HttpStatusCode.ServiceUnavailable`、HttpStatusCode.RequestTimeout 等；网络上有比较多的 HttpClientFactory 使用 Polly 来实现重试的内容，奈何已经习惯使用 Flurl 的人，要全部换回到 IHttpClient 的确有不方便的地方，因为本文使用 Flurl 的 Polly 来实现重试机制做一个整理；

## 不使用 Polly 来测试

1. 提供一个接口以便做请求测试

```C#
[Route("api/[controller]")]
[ApiController]
public class PollyController : ControllerBase
{
   private readonly ILogger<PollyController> _logger;

   public PollyController(ILogger<PollyController> logger)
   {
       _logger = logger;
   }

   // GET: api/<PollyController>
   [HttpGet]
   public IActionResult Get()
   {
       var random = new Random().Next(0, 8);
       switch (random)
       {
           case 0:
               _logger.LogInformation("About to serve a 404");
               return StatusCode(StatusCodes.Status404NotFound);

           case 1:
               _logger.LogInformation("About to serve a 503");
               return StatusCode(StatusCodes.Status503ServiceUnavailable);

           case 2:
               _logger.LogInformation("Sleeping for 10 seconds then serving a 504");
               Thread.Sleep(10000);
               _logger.LogInformation("About to serve a 504");
               return StatusCode(StatusCodes.Status504GatewayTimeout);

           default:
               _logger.LogInformation("About to correctly serve a 200 response");
               return Ok(new {time = DateTime.Now.ToLocalTime()});
       }
   }
}
```

2. 创建一个请求客户端

```C#
public class HomeController : Controller
{
   private readonly ILogger<HomeController> _logger;

   public HomeController(ILogger<HomeController> logger)
   {
       _logger = logger;
   }

   public async Task<IActionResult> Index()
   {
       try
       {
           var time = await "http://127.0.0.1:5000/api/polly"
               .GetJsonAsync();

           _logger.LogInformation($"App: success - {time.time}");
           return View(time.time);
       }
       catch (Exception e)
       {
           _logger.LogWarning($"App: failed - {e.Message}");
           throw;
       }
   }
}
```

3. 尝试请求，可以发现有很多请求失败的地方，这个情况很不理想，服务器有较大的机率不能正常的响应

```bash
info: SuppertRcsInterfaceTest.Controllers.PollyController[0]
     About to serve a 404
info: SuppertRcsInterfaceTest.Controllers.PollyController[0]
     About to correctly serve a 200 response
info: SuppertRcsInterfaceTest.Controllers.PollyController[0]
     About to correctly serve a 200 response
info: SuppertRcsInterfaceTest.Controllers.PollyController[0]
     About to correctly serve a 200 response
info: SuppertRcsInterfaceTest.Controllers.PollyController[0]
     About to serve a 503
info: SuppertRcsInterfaceTest.Controllers.PollyController[0]
     About to serve a 503
info: SuppertRcsInterfaceTest.Controllers.PollyController[0]
     About to correctly serve a 200 response
info: SuppertRcsInterfaceTest.Controllers.PollyController[0]
     About to serve a 404
```

> 针对这个情况有没有什么解决的办法呢，答案是肯定的，粗暴的想法就是失败了再重新做请求，直接在 Flurl 的返回结果中做这个逻辑处理会比较麻烦也不方便统一的管理，如此就找到了 Polly

## 使用 Polly 来测试

1. 首先安装 Polly, `Install-Package Polly`

2. 下面先给出 Polly 的简单介绍后接着给出`Policy`的代码片段

> Polly 的七种策略：重试、断路、超时、隔离、回退和缓存策略，本文使用到了重试、超时策略
>
> 重试（Retry）：出现故障自动重试，这个是常见的场景
>
> 断路（Circuit-breaker）：当系统遇到严重的问题时，快速回馈失败比让用户/调用者等待要好，限制系统出错的消耗，有助于系统恢复，比如，当我们去调用一个第三方的 API，有很长一段时间 API 都没有响应，可能对方服务器瘫痪了，如果我们的系统还不停地重试，不仅会加重系统的负担，还有可能导致系统其它任务受影响，因此，当系统出错的次数超过了指定的阈值，就得中断当前线程，等待一段时间后再继续；比如: `Policy.Handle<SomeException>().CircuitBreaker(2, TimeSpan.FromMinutes(1));`表示当系统出现两次某个异常时就停下来，等待 1 分钟后再继续，还可以在断路时定义中断的回调和重启的回调。
>
> 超时（Timeout）：当系统超过一定时间的等待，就可以判断不可能会有成功的结果；比如平时一个网络请求瞬间就完成了，如果有一次网络请求超过了 30 秒还没有完成，我们就可以判定不可能会返回成功的结果了，因此，我们需要设置系统的超时时间，避免系统长时间无谓的等待；比如：`Policy.Timeout(30, (context, span, task) => {// do something});`表示设置了超时时间不能超过 30 秒，否则就认为是错误的结果，并执行回调。
>
> 隔离（Bulkhead Isolation）：当系统的一处出现故障时，可能触发多个失败的调用，对资源有较大的消耗，下游系统出现故障可能导致上游的故障的调用，甚至可能蔓延到导致系统崩溃，所以要将可控的操作限制在一个固定大小的资源池中，以隔离有潜在可能相互影响的操作；比如：`Policy.Bulkhead(12, context => {// do something});`表示最多允许 12 个线程并发执行，如果执行被拒绝，则执行回调。
>
> 回退（Fallback）：有些错误无法避免，就要有备用的方案，当无法避免的错误发生时，我们要有一个合理的返回来代替失败；比如：`Policy.Handle<Whatever>().Fallback<UserAvatar>(() => UserAvatar.GetRandomAvatar());`表示当用户没有上传头像时，我们就给他一个默认头像。
>
> 缓存（Cache）：一般我们会把频繁使用且不会怎么变化的资源缓存起来，以提高系统的响应速度，如果不对缓存资源的调用进行封装，那么我们调用的时候就要先判断缓存中有没有这个资源，有的话就从缓存返回，否则就从资源存储的地方获取后缓存起来再返回，而且有时还要考虑缓存过期和如何更新缓存的问题；Polly 提供了缓存策略的支持，使得问题变得简单。
>
> 策略包（Policy Wrap）：一种操作会有多种不同的故障，而不同的故障处理需要不同的策略，这些不同的策略必须包在一起，作为一个策略包，才能应用在同一种操作上，这就是 Polly 的弹性特性，即各种不同的策略能够灵活地组合起来
>
> [更多...](https://github.com/App-vNext/Polly/wiki)

```c#
using System;
using System.Linq;
using System.Net;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;
using Flurl.Http.Configuration;
using Microsoft.Extensions.Logging;
using Polly;
using Polly.Retry;
using Polly.Timeout;
using Polly.Wrap;

namespace WithPollyClient.Services
{
   public class Policies
   {
       private readonly ILogger<Policies> _logger;

       public Policies(ILogger<Policies> logger)
       {
           _logger = logger;
       }

       private AsyncTimeoutPolicy<HttpResponseMessage> TimeoutPolicy
       {
           get
           {
               return Policy.TimeoutAsync<HttpResponseMessage>(3, (context, span, task) =>
               {
                   _logger.LogInformation($"Policy: Timeout delegate fired after {span.Seconds} seconds");
                   return Task.CompletedTask;
               });
           }
       }

       private AsyncRetryPolicy<HttpResponseMessage> RetryPolicy
       {
           get
           {
               HttpStatusCode[] retryStatus =
               {
                   HttpStatusCode.NotFound,
                   HttpStatusCode.ServiceUnavailable,
                   HttpStatusCode.RequestTimeout
               };
               return Policy
                   .HandleResult<HttpResponseMessage>(r => retryStatus.Contains(r.StatusCode))
                   .Or<TimeoutRejectedException>()
                   .WaitAndRetryAsync(new[]
                   {
                       // 表示重试3次，第一次1秒后重试，第二次2秒后重试，第三次4秒后重试
                       TimeSpan.FromSeconds(1),
                       TimeSpan.FromSeconds(2),
                       TimeSpan.FromSeconds(4)
                   }, (result, span, count, context) =>
                   {
                       _logger.LogInformation($"Policy: Retry delegate fired, attempt {count}");
                   });
           }
       }

       public AsyncPolicyWrap<HttpResponseMessage> PolicyStrategy =>
           Policy.WrapAsync(RetryPolicy, TimeoutPolicy);
   }

   public class PolicyHandler : DelegatingHandler
   {
       private readonly Policies _policies;

       public PolicyHandler(Policies policies)
       {
           _policies = policies;
       }

       protected override Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
       {
           return _policies.PolicyStrategy.ExecuteAsync(ct => base.SendAsync(request, ct), cancellationToken);
       }
   }

   public class PollyHttpClientFactory : DefaultHttpClientFactory
   {
       private readonly Policies _policies;

       public PollyHttpClientFactory(Policies policies)
       {
           _policies = policies;
       }

       public override HttpMessageHandler CreateMessageHandler()
       {
           return new PolicyHandler(_policies)
           {
               InnerHandler = base.CreateMessageHandler()
           };
       }
   }
}
```

3. 接下来在`Starup`中对 Flurl 进行配置

```c#
public void ConfigureServices(IServiceCollection services)
{
   services.AddControllersWithViews();
   services.AddSingleton<Policies>();
}

// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
   var policies = app.ApplicationServices.GetService<Policies>();
   FlurlHttp.Configure(setting =>
                       setting.HttpClientFactory = new PollyHttpClientFactory(policies));
   ......
```

4. 再次尝试请求，可以看到结果非常之理想

```c#
WithPollyClient.Services.Policies: Information: Policy: Retry delegate fired, attempt 1
WithPollyClient.Controllers.HomeController: Information: App: success - 2021/3/14 16:50:14
WithPollyClient.Services.Policies: Information: Policy: Retry delegate fired, attempt 1
WithPollyClient.Controllers.HomeController: Information: App: success - 2021/3/14 16:50:17
WithPollyClient.Controllers.HomeController: Information: App: success - 2021/3/14 16:50:22
WithPollyClient.Controllers.HomeController: Information: App: success - 2021/3/14 16:50:23
WithPollyClient.Services.Policies: Information: Policy: Retry delegate fired, attempt 1
WithPollyClient.Controllers.HomeController: Information: App: success - 2021/3/14 16:50:25
WithPollyClient.Controllers.HomeController: Information: App: success - 2021/3/14 16:50:31
WithPollyClient.Services.Policies: Information: Policy: Retry delegate fired, attempt 1
WithPollyClient.Controllers.HomeController: Information: App: success - 2021/3/14 16:50:34
WithPollyClient.Controllers.HomeController: Information: App: success - 2021/3/14 16:50:39
WithPollyClient.Services.Policies: Information: Policy: Retry delegate fired, attempt 1
WithPollyClient.Services.Policies: Information: Policy: Timeout delegate fired after 3 seconds
WithPollyClient.Services.Policies: Information: Policy: Retry delegate fired, attempt 2
WithPollyClient.Controllers.HomeController: Information: App: success - 2021/3/14 16:50:46
```

#### 富客户端中使用的情况

> 有时候呢，例如在`WPF`或者是其它的富客户端上面也会经常使用到 Flurl 的情况，如下

```c#
var time = await Policy
    .Handle<FlurlHttpException>()
    .OrResult<IFlurlResponse>(r => !r.ResponseMessage.IsSuccessStatusCode)
    .WaitAndRetryAsync(new[]
                       {
                           TimeSpan.FromSeconds(1),
                           TimeSpan.FromSeconds(2),
                           TimeSpan.FromSeconds(4)
                       }, (result, span, count, context) =>
                       {
                           _logger.LogInformation(count.ToString());
                       })
    .ExecuteAsync(() => "http://127.0.0.1:5000/api/polly".WithTimeout(3).GetAsync())
    .ReceiveJson();

_logger.LogInformation($"App: success - {time.time}");
return View(time.time);
```
