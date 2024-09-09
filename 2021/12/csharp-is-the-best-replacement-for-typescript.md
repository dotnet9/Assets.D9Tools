---
title: C# 是 TypeScript 的最佳替补？
slug: csharp-is-the-best-replacement-for-typescript
description: TypeScript非常优秀。它完美地结合了强类型和快速开发，因此非常好用，我在许多情况下都会默认选择这个库。但是，世上没有完美的语言，有些情况下TypeScript并不是最合适的工具：
date: 2021-12-27 20:53:19
copyright: Reprinted
author: CSDN
originalTitle: C# 是 TypeScript 的最佳替补？
originalLink: https://mp.weixin.qq.com/s/yXShXwe9O7uzOBTRqMPd7Q
draft: False
cover: https://img1.dotnet9.com/2021/12/cover_44.jpg
categories: .NET
tags: 
    - C#
    - TypeScript
---

> 作者 | Nate Hill
>
> 译者 | 弯月
>
> 出品 | CSDN（ID：CSDNnews）

TypeScript 非常优秀。它完美地结合了强类型和快速开发，因此非常好用，我在许多情况下都会默认选择这个库。但是，世上没有完美的语言，有些情况下 TypeScript 并不是最合适的工具：

1. 性能至关重要（例如实时通信、视频游戏）
2. 需要与原生代码（如 C/C++或 Rust）交互
3. 需要更严格的类型系统（例如金融系统）

对于这些情况，TypeScript 开发人员最好还是选用其他语言。C#、Go 和 Java 都是非常好的选择。它们的速度远超 TypeScript，每种语言都有自己的长处。C#能与 TypeScript 配合得很好，我来解释一下为什么。

![图源：CSDN 付费下载自东方 IC](https://img1.dotnet9.com/2021/12/cover_44.jpg)

## 1. TypeScript 就是添加了 C# 的 JavaScript

C#能与 TypeScript 配合得很好，因为它们看上去就像是同一种语言。两者都是由 Anders Hejlsberg 设计的，而且从许多方面来看，**TypeScript 就是添加了 C#的 JavaScript**。它们的特性和语法都很相似，因此在同一个项目中结合使用二者非常容易。更重要的是，C#的语言与 TypeScript 很相似，因此开发人员阅读和编写代码也非常轻松。
相反，Go 是一种完全不同的语言：没有类，没有继承，没有异常，没有包级别的封装（只有类级别的封装），而且语法也完全不同。当然这并不一定是坏事，但开发人员的确需要重新思考并用不同的方式设计代码，因此，同时使用 Go 和 TypeScript 是比较困难的。不过，Java 与 C#很相似，但依然缺乏许多 C#和 TypeScript 都有的功能。

## 2. C#和 TypeScript 的相似之处

也许你已经知道，C#和 TypeScript 有很多相似之处，如基于 C 的语法、类、接口、泛型等。下面，我来详细列举一下二者的相似之处：

- 2.1 async/await
- 2.2 lambda 表达式和函数式数组方法
- 2.3 用于处理空的操作符（?，!，??）
- 2.4 解构
- 2.5 命令行界面（CLI）
- 2.6 基本功能（类、泛型、错误和枚举）

### 2.1 async/await

首先，C#和 JavaScript 都使用 async/await 来处理异步代码。在 JavaScript 中，异步操作用 Promise 表示，而应用程序可以 await 一个异步操作结束。C#中的 Promise 其实是 Task，概念上与 Promise 完全相同，也有相应的方法。下面的例子演示了两种语言中 async/await 的用法：

**TypeScript 中 async/await 的例子：**

```TypeScript
async function fetchAndWriteToFile(url: string, filePath:string): Promise<string> {
  // fetch() returns aPromise
  const response = awaitfetch(url);
  const text = awaitresponse.text();
  // By the way, we'reusing Deno (https://deno.land)
  awaitDeno.writeTextFile(filePath, text);
  return text;
}
```

**C#中 async/await 的例子：**

```C#
using System.IO;
using System.Net.Http;
using System.Threading.Tasks;

async Task<string> FetchAndWriteToFile(string url, stringfilePath) {
  // HttpClient.GetAsync()returns a Task
  var response = await newHttpClient().GetAsync(url);
  var text = awaitresponse.Content.ReadAsStringAsync();
  awaitFile.WriteAllTextAsync(filePath, text);
  return text;
}
```

下面是`JavaScript`的`Promise` API 与等价的`C#` `Task` API：

| JavaScript API           | 等价的 C# API              |
| ------------------------ | -------------------------- |
| Promise.all()            | Task.WaitAll()             |
| Promise.resolve()        | Task.FromResult()          |
| Promise.reject()         | Task.FromException()       |
| Promise.prototype.then() | Task.ContinueWith()        |
| new Promise()            | new TaskCompletionSource() |

### 2.2 Lambda 表达式和函数式数组方法

`C#`和`JavaScript`都用熟悉的`=>`语法（即`箭头函数`）来表示`lambda表达式`。下面是`TypeScript`和`C#`的比较：

**TypeScript 中使用 lambda 表达式：**

```TypeScript
const months = ['January', 'February', 'March', 'April'];
const shortMonthNames = months.filter(month => month.length< 6);
const monthAbbreviations = months.map(month =>month.substr(0, 3));
const monthStartingWithF = months.find(month => {
  returnmonth.startsWith('F');
});
```

**C#中使用 lambda 表达式：**

```C#

using System.Collections.Generic;
using System.Linq;

var months = new List<string> {"January","February", "March", "April"};
var shortMonthNames = months.Where(month => month.Length <6);
var monthAbbreviations = months.Select(month =>month.Substring(0, 3));
var monthStartingWithF = months.Find(month => {
  returnmonth.StartsWith("F");
});
```

上述示例演示了`C#`的`System.Linq`命名空间中的一些方法，相当于`JavaScript`的`函数式数组方法`。下面是`JavaScript`的`数组方法`与等价的`C# Linq`方法：

| JavaScript API              | 等价的 C# API          |
| --------------------------- | ---------------------- |
| Array.prototype.filter()    | Enumerable.Where()     |
| Array.prototype.map()       | Enumerable.Select()    |
| Array.prototype.reduce()    | Enumerable.Aggregate() |
| Array.prototype.every()     | Enumerable.All()       |
| Array.prototype.find()      | List.Find()            |
| Array.prototype.findIndex() | List.FindIndex()       |

### 2.3 处理空操作符

C#和 TypeScript 处理空的特性也一样：

| Feature name        | Syntax                     | Documentation links                                                                                                                                                                                                                              |
| ------------------- | -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Optional properties | property?                  | TS ：https://www.typescriptlang.org/docs/handbook/2/objects.html#optional-properties，<br>C#：https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/nullable-reference-types                                           |
| Non-null assertion  | object!.property           | TS：https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-0.html#non-null-assertion-operator，<br>C#：https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/null-forgiving                               |
| Optional chaining   | object?.property           | JS ：https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining，<br>C#：https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/member-access-operators#null-conditional-operators--and- |
| Nullish coalescing  | object ?? alternativeValue | JS：https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing_operator，<br>C#：https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/null-coalescing-operator                        |

### 2.4 解构

尽管 C#默认不支持数组或类的解构，但它支持 Tuple 和 Record 的解构，用户也可以为自定义类型定义解构。下面是 TypeScript 和 C#中解构的例子：

**TypeScript 中解构的例子：**

```TypeScript
const author = { firstName: 'Kurt', lastName: 'Vonnegut' };
// Destructuring an object:
const { firstName, lastName } = author;

const cityAndCountry = ['Indianapolis', 'United States'];
// Destructuring an array:
const [city, country] = cityAndCountry;
```

**C#中解构的例子：**

```C#

using System;

var author = new Author("Kurt", "Vonnegut");
// Deconstructing a record:
var (firstName, lastName) = author;

var cityAndCountry = Tuple.Create("Indianapolis","United States");
// Deconstructing a tuple:
var (city, country) = cityAndCountry;

// Define the Author record used above
record Author(string FirstName, string LastName);
```

### 2.5 命令行界面（CLI）

我的开发方式是使用文本编辑器编写代码，然后在终端运行命令，构建并运行。对于`TypeScript`，这意味着需要`使用node或deno命令行界面（CLI）`。`C#`也有`类似的CLI`，名为`dotnet`（由 C#的.NET 运行时得名）。下面是使用`dotnet CLI`的一些例子：

```shell
mkdir app && cd app
# Create a new console application
# List of available app templates:https://docs.microsoft.com/dotnet/core/tools/dotnet-new
dotnet new console
# Run the app
dotnet run
# Run tests (don't feel bad if you haven't written those)
dotnet test
# Build the app as a self-contained
# single file application for Linux.
dotnet publish -c Release -r linux-x64
```

### 2.6 基本功能（类、泛型、错误和枚举）

这些是 TypeScript 和 C#之间更基本的相似性。下面的例子是有关这几个方面的介绍：

**TypeScript 类的示例：**

```TypeScript
import { v4 as uuidv4 } from'https://deno.land/std/uuid/mod.ts';

enum AccountType {
  Trial,
  Basic,
  Pro
}

interface Account {
  id: string;
  type: AccountType;
  name: string;
}

interface Database<T> {
  insert(item: T):Promise;
  get(id: string):Promise<T>;
}

class AccountManager {

  constructor(database:Database<Account>) {
    this._database =database;
  }

  asynccreateAccount(type: AccountType, name: string) {
    try {
      const account = {
        id: uuidv4(),
        type,
        name;
      };
      awaitthis._database.insert(account);
    } catch (error) {
        console.error(`Anunexpected error occurred while creating an account. Name: ${name}, Error:${error}`);
    }
  }

  private _database:Database<Account>;
}
```

**C#类的示例：**

```C#
using System;
using System.Threading.Tasks;

enum AccountType {
  Trial,
  Basic,
  Pro
}

record Account(string Id, AccountType Type, string Name);

interface IDatabase<T> {
  Task Insert(T item);
  Task<T> Get(stringid);
}

class AccountManager {

  publicAccountManager(IDatabase<Account> database) {
    _database = database;
  }

  public async voidCreateAccount(AccountType type, string name) {
    try {
      var account = newAccount(
       Guid.NewGuid().ToString(),
        type,
        name
      );
      await_database.Insert(account)
    } catch (Exceptionexception) {
     Console.WriteLine($"An unexpected error occurred while creating anaccount. Name: {name}, Exception: {exception}");
    }
  }

  IDatabase<Account>_database;
}
```

## 3. C#的其他优势

与 TypeScript 相似并不是 C#的唯一优点，它还有其他优点：

- 3.1 与原生代码结合更容易
- 3.2 事件
- 3.3 其他功能

### 3.1 与原生代码结合

`C#`的最大优势之一就是它可以`深入原生代码`。本文开头提到，`TypeScript`并`不擅长与C/C++代码结合`。`Node.js`有一个`支持原生C/C++的插件`，名为`Node-API`，但是它需要为原生函数编写额外的`C++包裹器`，将原生类型转换成`JavaScript`对象，或相反，`类似于JNI`的工作方式。而`C#`可以直接调用`原生函数`，只需把库放到应用程序的 bin 目录下，然后将 API 定义为 C#中的外部函数即可。然后就能像`C#`函数一样使用外部函数，`.NET运行时`会处理好 C#数据类型与原生数据类型之间的转换。例如，如果原生库导出了下面的 C 函数：

```C
int countOccurrencesOfCharacter(char *string, char character) {
    int count = 0;
    for (int i = 0;string[i] != '\0'; i++) {
        if (string[i] ==character) {
            count++;
        }
    }
    return count;
}
```

那么可像下面这样从 C#中调用：

```C#
using System;
using System.Runtime.InteropServices;

var count = MyLib.countOccurrencesOfCharacter("C# is prettyneat, eh?", 'e');
// Prints "3"
Console.WriteLine(count);

class MyLib {
    // Just placeMyLibraryName.so in the app's bin folder
   [DllImport("MyLibraryName")]
    public static externint countOccurrencesOfCharacter(string str, char character);
}
```

这种方法可以通过`C`连接访问任何动态库（`.so`、`.dll`或`.dylib`），也就是说，你可以轻松地调用`C`、`C++`、`Rust`、`Go`或`其他语言`编写的代码，只要编译成`机器码`即可。原生交互的其他应用还有：

- 将指针作为 IntPtr 传给原生对象
- 利用`GetFunctionPointerForDelegate()`将`C#`方法作为函数指针传给原生函数
- 使用`Marshal.PtrToStringAnsi()`将`C`字符串转换为`C#`字符串
- 转换结构和数组

### 3.2 事件

`C#`的一个独特的特性是，提供了`一流的事件支持`。在`TypeScript`中，你可以实现`addEventListener()`方法，让客户端`监听事件`，而`C#`有`event关键字`，可以用来`定义事件`，并通过简单的语法将事件通知给所有监听者（而不需要像`TypeScript`那样手动遍历所有事件监听者并在`try/catch`块中执行）。例如，我们可以让`Connection`类定义一个`MessageReceived`事件，如下所示：

```C#
class Connection {
    // AnAction<string> is a callback that accepts a string parameter.
    public eventAction<string> MessageReceived;
}
```

使用`Connection`代码可以通过`+=`操作符给`MessageReceived`添加一个处理函数，如下：

```C#
var connection = new Connection();
connection.MessageReceived += (message) => {
   Console.WriteLine("Message was received: " + message);
};
```

而`Connection`类可以在内部调用`MessageReceived`，为所有监听者触发`MessageReceived`事件：

```C#
// Raise the MessageReceived event
MessageReceived?.Invoke(message);
```

## 4. 其他优势

1. **性能：** `C#`很快。`C#`的`ASP.NET(Core) Web`框架一直在`Techempower`的评测中`名列前茅`，而`C#`的`.NET CoreCLR运行时`的性能每个主要版本都在提高。`C#`拥有优良性能的原因之一是，通过使用结构而不是类，应用程序`可以最小化甚至完全消除垃圾回收`。因此，`C#`在`视频游戏编程`中非常流行。
2. **游戏和混合现实：** `C#`是游戏开发最流行的语言之一，像`Unity`、`Godot`甚至`Unreal`游戏引擎都使用了`C#`。`C#`在**混合现实**中也很流行，因为`VR`和`AR`应用程序都是用`Unity`编写的。
3. 由于`C#`拥有第一方库、工具和文档，因此一些任务非常容易实现，比如，在`C#`中创建`gRPC客户端`要比`TypeScript`方便得多。相反，在`Node.js`中使用`TypeScript`时，就必须找出正确的模块和工具的组合，才能正确地生成`JavaScript gRPC客户端`，以及相应的`TypeScript`类型。
4. 高级功能：`C#`有许多其他语言没有的功能，如`运算符重载`、`析构函数`等。

## 5. 总结

如前所述，`世上没有完美的语言`。在设计语言时总要`有所权衡`，所以一些语言的速度更快，但使用难度会增加（例如`Rust`的借出检查）。另一方面，一些语言非常易用，但通常性能的优化难度就会增加（例如`JavaScript`的动态语言特性）。正因如此，我相信`掌握一组相似的语言会非常有用`：这些语言`分别有各自的长处`，但都很相似，而且能互相配合。例如，下面是我选择的一组语言：

### 5.1 TypeScript

- 最高层的语言，开发速度最快
- 性能并非最佳，但适用于大多数应用
- 不太适合与原生代码结合

### 5.2 C#

- 仍然是高级语言，支持垃圾回收，所以很容易使用，尽管并不如`TypeScript`那么容易。
- 从速度和内存占用量来看，其性能都优于 `TypeScript`
- 最重要的是，能够与底层很好地结合

### C++

- 开发难度较大（例如需要手动内存管理），因此开发速度会慢很多
- 但运行时的性能最佳！而且随处可用，能与许多已有的软件相结合
- 很像`C#`，而且标准库很好，但也有许多陷阱（大多数与内存管理有关）。我更希望使用`Rust`，因为它的内存安全性更好，但我的许多工作都要与已有的`C++`代码结合，因此使用`C++`会更容易。

参考链接：https://nate.org/csharp-and-typescript
