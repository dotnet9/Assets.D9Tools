---
title: Dapper in .NET Core
slug: Dapper-in-dotNET-Core
description: 关于什么是Dapper（详细入口），在此不做赘述；本文仅对Dapper在.Net Core中的使用作扼要说明，所陈代码以示例讲解为主，乃抛砖引玉，开发者可根据自身需要进行扩展和调整
date: 2022-01-10 22:12:04
copyright: Reprinted
author: 白云任去留
originalTitle: Dapper in .NET Core
originalLink: cnblogs.com/ang/p/13620452.html
draft: False
cover: https://img1.dotnet9.com/2022/01/cover_08.jpg
categories: .NET
tags: 
    - C#
    - .NET
    - Dapper
    - ORM
---

**目录**

- 一、前言
- 二、Dapper 环境搭建
- 三、Dapper 封装
  - 定义 DapperDBContext 类
  - 异步分页构建（PageAsync）
  - 定义工作单元与事务
  - 定义数据仓储
  - 数据库连接
- 四、Dapper 使用

## 一、前言

关于什么是 Dapper（详细入口），在此不做赘述；本文仅对 Dapper 在.Net Core 中的使用作扼要说明，所陈代码以示例讲解为主，乃抛砖引玉，开发者可根据自身需要进行扩展和调整；其中如有疏漏之处，望不吝斧正。

## 二、Dapper 环境搭建

当前以.Net Core WebAPI 或 MVC 项目为例，框架版本为.NET 5.0，相关 NuGet 包引用如下：

```shell
Install-Package Dapper
Install-Package Dapper.Contrib
Install-Package Dapper.SqlBuilder
Install-Package System.Data.SqlClient
```

其中 Dapper.Contrib 和 Dapper.SqlBuilder 为 Dapper 的扩展，当然，Dapper 的扩展还有如 Dapper.Rainbow 等其他包，根据自身需要引用，对相关引用作下说明：

- Dapper：不言而喻；
- Dapper.Contrib：可使用对象进行数据表的增删改查，免却 SQL 语句的编写；
- Dapper.SqlBuilder：可以方便动态构建 SQL 语句，如 Join、SELECT、Where、OrderBy 等等；
- System.Data.SqlClient：由于示例数据库为 Sql Server，如 MySql 则引用 MySql.Data；

对于 Dapper.Contrib 实体配置选项，以 Product 类为例，作扼要说明如下：

```C#
[Table("Product")]
public class Product
{
    [Key]
    public int Id { get; set; }
    public string Name{ get; set; }
    public string Description { get; set; }
    public decimal Price { get; set; }
    public DateTime CreateTime { get; set; }
}
```

对于实体配置项，有如下几个主要项：

- Table：指定数据库表名，可忽略；
- Key：指定为自动增长主键；
- ExplicitKey：指定非自动增长主键，如 guid；
- Computed：计算列属性，Insert、Update 操作将忽略此列；
- Write：是否可写入，true/false，如[Write(false)]，false 时 Insert、Update 操作将忽略此列，比如可扩展局部类作数据表额外查询字段使用；

对于数据表对象实体，可结合 T4 模板生成即可。

## 三、Dapper 封装

关于 Dapper 数据访问，这里参考 Github 上的某示例（入口：https://github.com/EloreTec/UnitOfWorkWithDapper），作修改调整封装如下：

### 定义 DapperDBContext 类

```C#
public abstract class DapperDBContext : IContext
    {
        private IDbConnection _connection;
        private IDbTransaction _transaction;
        private int? _commandTimeout = null;
        private readonly DapperDBContextOptions _options;

        public bool IsTransactionStarted { get; private set; }

        protected abstract IDbConnection CreateConnection(string connectionString);

        protected DapperDBContext(IOptions<DapperDBContextOptions> optionsAccessor)
        {
            _options = optionsAccessor.Value;

            _connection = CreateConnection(_options.Configuration);
            _connection.Open();

            DebugPrint("Connection started.");
        }

        #region Transaction

        public void BeginTransaction()
        {
            if (IsTransactionStarted)
                throw new InvalidOperationException("Transaction is already started.");

            _transaction = _connection.BeginTransaction();
            IsTransactionStarted = true;

            DebugPrint("Transaction started.");
        }

        public void Commit()
        {
            if (!IsTransactionStarted)
                throw new InvalidOperationException("No transaction started.");

            _transaction.Commit();
            _transaction = null;

            IsTransactionStarted = false;

            DebugPrint("Transaction committed.");
        }

        public void Rollback()
        {
            if (!IsTransactionStarted)
                throw new InvalidOperationException("No transaction started.");

            _transaction.Rollback();
            _transaction.Dispose();
            _transaction = null;

            IsTransactionStarted = false;

            DebugPrint("Transaction rollbacked and disposed.");
        }

        #endregion Transaction

        #region Dapper.Contrib.Extensions

        public async Task<T> GetAsync<T>(int id) where T : class, new()
        {
            return await _connection.GetAsync<T>(id, _transaction, _commandTimeout);
        }

        public async Task<T> GetAsync<T>(string id) where T : class, new()
        {
            return await _connection.GetAsync<T>(id, _transaction, _commandTimeout);
        }

        public async Task<IEnumerable<T>> GetAllAsync<T>() where T : class, new()
        {
            return await _connection.GetAllAsync<T>();
        }

        public long Insert<T>(T model) where T : class, new()
        {
            return _connection.Insert<T>(model, _transaction, _commandTimeout);
        }

        public async Task<int> InsertAsync<T>(T model) where T : class, new()
        {
            return await _connection.InsertAsync<T>(model, _transaction, _commandTimeout);
        }
        public bool Update<T>(T model) where T : class, new()
        {
            return _connection.Update<T>(model, _transaction, _commandTimeout);
        }

        public async Task<bool> UpdateAsync<T>(T model) where T : class, new()
        {
            return await _connection.UpdateAsync<T>(model, _transaction, _commandTimeout);
        }

        public async Task<Page<T>> PageAsync<T>(long pageIndex, long pageSize, string sql, object param = null)
        {
            DapperPage.BuildPageQueries((pageIndex - 1) * pageSize, pageSize, sql, out string sqlCount, out string sqlPage);

            var result = new Page<T>
            {
                CurrentPage = pageIndex,
                ItemsPerPage = pageSize,
                TotalItems = await _connection.ExecuteScalarAsync<long>(sqlCount, param)
            };
            result.TotalPages = result.TotalItems / pageSize;

            if ((result.TotalItems % pageSize) != 0)
                result.TotalPages++;

            result.Items = await _connection.QueryAsync<T>(sqlPage, param);
            return result;
        }


        #endregion


        #region Dapper Execute & Query


        public int ExecuteScalar(string sql, object param = null, CommandType commandType = CommandType.Text)
        {
            return _connection.ExecuteScalar<int>(sql, param, _transaction, _commandTimeout, commandType);
        }

        public async Task<int> ExecuteScalarAsync(string sql, object param = null, CommandType commandType = CommandType.Text)
        {
            return await _connection.ExecuteScalarAsync<int>(sql, param, _transaction, _commandTimeout, commandType);
        }
        public int Execute(string sql, object param = null, CommandType commandType = CommandType.Text)
        {
            return _connection.Execute(sql, param, _transaction, _commandTimeout, commandType);
        }

        public async Task<int> ExecuteAsync(string sql, object param = null, CommandType commandType = CommandType.Text)
        {
            return await _connection.ExecuteAsync(sql, param, _transaction, _commandTimeout, commandType);
        }

        public IEnumerable<T> Query<T>(string sql, object param = null, CommandType commandType = CommandType.Text)
        {
            return _connection.Query<T>(sql, param, _transaction, true, _commandTimeout, commandType);
        }

        public async Task<IEnumerable<T>> QueryAsync<T>(string sql, object param = null, CommandType commandType = CommandType.Text)
        {
            return await _connection.QueryAsync<T>(sql, param, _transaction, _commandTimeout, commandType);
        }

        public T QueryFirstOrDefault<T>(string sql, object param = null, CommandType commandType = CommandType.Text)
        {
            return _connection.QueryFirstOrDefault<T>(sql, param, _transaction, _commandTimeout, commandType);
        }

        public async Task<T> QueryFirstOrDefaultAsync<T>(string sql, object param = null, CommandType commandType = CommandType.Text)
        {
            return await _connection.QueryFirstOrDefaultAsync<T>(sql, param, _transaction, _commandTimeout, commandType);
        }
        public IEnumerable<TReturn> Query<TFirst, TSecond, TReturn>(string sql, Func<TFirst, TSecond, TReturn> map, object param = null, string splitOn = "Id", CommandType commandType = CommandType.Text)
        {
            return _connection.Query(sql, map, param, _transaction, true, splitOn, _commandTimeout, commandType);
        }

        public async Task<IEnumerable<TReturn>> QueryAsync<TFirst, TSecond, TReturn>(string sql, Func<TFirst, TSecond, TReturn> map, object param = null, string splitOn = "Id", CommandType commandType = CommandType.Text)
        {
            return await _connection.QueryAsync(sql, map, param, _transaction, true, splitOn, _commandTimeout, commandType);
        }

        public async Task<SqlMapper.GridReader> QueryMultipleAsync(string sql, object param = null, CommandType commandType = CommandType.Text)
        {
            return await _connection.QueryMultipleAsync(sql, param, _transaction, _commandTimeout, commandType);
        }

        #endregion Dapper Execute & Query

        public void Dispose()
        {
            if (IsTransactionStarted)
                Rollback();

            _connection.Close();
            _connection.Dispose();
            _connection = null;

            DebugPrint("Connection closed and disposed.");
        }

        private void DebugPrint(string message)
        {
#if DEBUG
            Debug.Print(">>> UnitOfWorkWithDapper - Thread {0}: {1}", Thread.CurrentThread.ManagedThreadId, message);
#endif
        }
    }
```

```C#
public class DapperDBContextOptions : IOptions<DapperDBContextOptions>
    {
        public string Configuration { get; set; }

        DapperDBContextOptions IOptions<DapperDBContextOptions>.Value
        {
            get { return this; }
        }
    }

 public interface IContext : IDisposable
    {
        bool IsTransactionStarted { get; }

        void BeginTransaction();

        void Commit();

        void Rollback();
    }
```

以上代码涵盖了 Dapper 访问数据库的基本操作，分同步和异步，其中大部分不作赘述，着重说下分页部分；

### 异步分页构建（PageAsync）

分页这里为方便调用，只需传入要查询的 Sql 语句（如：SELECT \* FROM Table，必须带 Order BY）、页索引、页大小即可；

至于具体如何构建的，这里参照某小型 ORM 工具 PetaPoco，抽取相关代码如下，有兴趣的同学也可以自行改造：

```C#
public class Page<T>
    {
        /// <summary>
        /// The current page number contained in this page of result set
        /// </summary>
        public long CurrentPage { get; set; }

        /// <summary>
        /// The total number of pages in the full result set
        /// </summary>
        public long TotalPages { get; set; }

        /// <summary>
        /// The total number of records in the full result set
        /// </summary>
        public long TotalItems { get; set; }

        /// <summary>
        /// The number of items per page
        /// </summary>
        public long ItemsPerPage { get; set; }

        /// <summary>
        /// The actual records on this page
        /// </summary>
        public IEnumerable<T> Items { get; set; }
        //public List<T> Items { get; set; }
    }
    public class DapperPage
    {
        public static void BuildPageQueries(long skip, long take, string sql, out string sqlCount, out string sqlPage)
        {
            // Split the SQL
            if (!PagingHelper.SplitSQL(sql, out PagingHelper.SQLParts parts))
                throw new Exception("Unable to parse SQL statement for paged query");

            sqlPage = BuildPageSql.BuildPageQuery(skip, take, parts);
            sqlCount = parts.sqlCount;
        }
    }

    static class BuildPageSql
    {
        public static string BuildPageQuery(long skip, long take, PagingHelper.SQLParts parts)
        {
            parts.sqlSelectRemoved = PagingHelper.rxOrderBy.Replace(parts.sqlSelectRemoved, "", 1);
            if (PagingHelper.rxDistinct.IsMatch(parts.sqlSelectRemoved))
            {
                parts.sqlSelectRemoved = "peta_inner.* FROM (SELECT " + parts.sqlSelectRemoved + ") peta_inner";
            }
            var sqlPage = string.Format("SELECT * FROM (SELECT ROW_NUMBER() OVER ({0}) peta_rn, {1}) peta_paged WHERE peta_rn>{2} AND peta_rn<={3}",
                                    parts.sqlOrderBy ?? "ORDER BY (SELECT NULL)", parts.sqlSelectRemoved, skip, skip + take);
            //args = args.Concat(new object[] { skip, skip + take }).ToArray();

            return sqlPage;
        }

        //SqlServer 2012及以上
        public static string BuildPageQuery2(long skip, long take, PagingHelper.SQLParts parts)
        {
            parts.sqlSelectRemoved = PagingHelper.rxOrderBy.Replace(parts.sqlSelectRemoved, "", 1);
            if (PagingHelper.rxDistinct.IsMatch(parts.sqlSelectRemoved))
            {
                parts.sqlSelectRemoved = "peta_inner.* FROM (SELECT " + parts.sqlSelectRemoved + ") peta_inner";
            }

            var sqlOrderBy = parts.sqlOrderBy ?? "ORDER BY (SELECT NULL)";
            var sqlPage = $"SELECT {parts.sqlSelectRemoved} {sqlOrderBy} OFFSET {skip} ROWS FETCH NEXT {take} ROWS ONLY";
            return sqlPage;
        }
    }

    static class PagingHelper
    {
        public struct SQLParts
        {
            public string sql;
            public string sqlCount;
            public string sqlSelectRemoved;
            public string sqlOrderBy;
        }

        public static bool SplitSQL(string sql, out SQLParts parts)
        {
            parts.sql = sql;
            parts.sqlSelectRemoved = null;
            parts.sqlCount = null;
            parts.sqlOrderBy = null;

            // Extract the columns from "SELECT <whatever> FROM"
            var m = rxColumns.Match(sql);
            if (!m.Success)
                return false;

            // Save column list and replace with COUNT(*)
            Group g = m.Groups[1];
            parts.sqlSelectRemoved = sql.Substring(g.Index);

            if (rxDistinct.IsMatch(parts.sqlSelectRemoved))
                parts.sqlCount = sql.Substring(0, g.Index) + "COUNT(" + m.Groups[1].ToString().Trim() + ") " + sql.Substring(g.Index + g.Length);
            else
                parts.sqlCount = sql.Substring(0, g.Index) + "COUNT(*) " + sql.Substring(g.Index + g.Length);


            // Look for the last "ORDER BY <whatever>" clause not part of a ROW_NUMBER expression
            m = rxOrderBy.Match(parts.sqlCount);
            if (!m.Success)
            {
                parts.sqlOrderBy = null;
            }
            else
            {
                g = m.Groups[0];
                parts.sqlOrderBy = g.ToString();
                parts.sqlCount = parts.sqlCount.Substring(0, g.Index) + parts.sqlCount.Substring(g.Index + g.Length);
            }

            return true;
        }

        public static Regex rxColumns = new Regex(@"\A\s*SELECT\s+((?:\((?>\((?<depth>)|\)(?<-depth>)|.?)*(?(depth)(?!))\)|.)*?)(?<!,\s+)\bFROM\b", RegexOptions.IgnoreCase | RegexOptions.Multiline | RegexOptions.Singleline | RegexOptions.Compiled);
        public static Regex rxOrderBy = new Regex(@"\bORDER\s+BY\s+(?!.*?(?:\)|\s+)AS\s)(?:\((?>\((?<depth>)|\)(?<-depth>)|.?)*(?(depth)(?!))\)|[\w\(\)\.])+(?:\s+(?:ASC|DESC))?(?:\s*,\s*(?:\((?>\((?<depth>)|\)(?<-depth>)|.?)*(?(depth)(?!))\)|[\w\(\)\.])+(?:\s+(?:ASC|DESC))?)*", RegexOptions.RightToLeft | RegexOptions.IgnoreCase | RegexOptions.Multiline | RegexOptions.Singleline | RegexOptions.Compiled);
        public static Regex rxDistinct = new Regex(@"\ADISTINCT\s", RegexOptions.IgnoreCase | RegexOptions.Multiline | RegexOptions.Singleline | RegexOptions.Compiled);
    }
```

对于构建分页语句，分别示例 BuildPageQuery 和 BuildPageQuery2，前者为通过 ROW_NUMBER 进行分页（针对 SqlServer2005、2008），后者通过 OFFSET、FETCH 分页（针对 SqlServer2012 及以上版本），相关辅助操作类一览便知，如果使用 MySql 数据库，可酌情自行封装；

至于 Where 查询的进一步封装，有兴趣的也可兑 Dapper lamada 查询进行扩展。

### 定义工作单元与事务

```C#
public interface IUnitOfWork : IDisposable
    {
        void SaveChanges();
    }

    public interface IUnitOfWorkFactory
    {
        IUnitOfWork Create();
    }

public class UnitOfWork : IUnitOfWork
    {
        private readonly IContext _context;

        public UnitOfWork(IContext context)
        {
            _context = context;
            _context.BeginTransaction();
        }

        public void SaveChanges()
        {
            if (!_context.IsTransactionStarted)
                throw new InvalidOperationException("Transaction have already been commited or disposed.");

            _context.Commit();
        }

        public void Dispose()
        {
            if (_context.IsTransactionStarted)
                _context.Rollback();
        }
    }

public class DapperUnitOfWorkFactory : IUnitOfWorkFactory
    {
        private readonly DapperDBContext _context;

        public DapperUnitOfWorkFactory(DapperDBContext context)
        {
            _context = context;
        }

        public IUnitOfWork Create()
        {
            return new UnitOfWork(_context);
        }
    }
```

### 定义数据仓储

```C#
#region Product
    public partial interface IProductRepository
    {
        Task<Product> GetAsync(int id);

        Task<IEnumerable<Product>> GetAllAsync();

        long Insert(Product model);

        Task<int> InsertAsync(Product model);

        bool Update(Product model);

        Task<bool> UpdateAsync(Product model);

        int Count(string where, object param = null);

        Task<int> CountAsync(string where, object param = null);

        bool Exists(string where, object param = null);

        Task<bool> ExistsAsync(string where, object param = null);

        Product FirstOrDefault(string where, object param = null);

        Task<Product> FirstOrDefaultAsync(string where, object param = null);

        T FirstOrDefault<T>(string sql, object param = null);

        Task<T> FirstOrDefaultAsync<T>(string sql, object param = null);

        IEnumerable<Product> Fetch(SqlBuilder where);

        Task<IEnumerable<Product>> FetchAsync(SqlBuilder where);

        IEnumerable<T> Fetch<T>(string sql, SqlBuilder where, bool orderBy = true);

        Task<IEnumerable<T>> FetchAsync<T>(string sql, SqlBuilder where, bool orderBy = true);

        Task<Page<Product>> PageAsync(long pageIndex, long pageSize, SqlBuilder builder);

        Task<Page<T>> PageAsync<T>(string sql, long pageIndex, long pageSize, SqlBuilder builder);

        Task<SqlMapper.GridReader> QueryMultipleAsync(string sql, object param = null);
    }

    public partial class ProductRepository : IProductRepository
    {
        private readonly DapperDBContext _context;
        public ProductRepository(DapperDBContext context)
        {
            _context = context;
        }

        public async Task<Product> GetAsync(int id)
        {
            return await _context.GetAsync<Product>(id);
        }

        public async Task<IEnumerable<Product>> GetAllAsync()
        {
            return await _context.GetAllAsync<Product>();
        }

        public long Insert(Product model)
        {
            return _context.Insert<Product>(model);
        }

        public async Task<int> InsertAsync(Product model)
        {
            return await _context.InsertAsync<Product>(model);
        }

        public bool Update(Product model)
        {
            return _context.Update<Product>(model);
        }

        public async Task<bool> UpdateAsync(Product model)
        {
            return await _context.UpdateAsync<Product>(model);
        }

        public int Count(string where, object param = null)
        {
            string strSql = $"SELECT COUNT(1) FROM Product {where}";
            return _context.ExecuteScalar(strSql, param);
        }

        public async Task<int> CountAsync(string where, object param = null)
        {
            string strSql = $"SELECT COUNT(1) FROM Product {where}";
            return await _context.ExecuteScalarAsync(strSql, param);
        }

        public bool Exists(string where, object param = null)
        {
            string strSql = $"SELECT TOP 1 1 FROM Product {where}";
            var count = _context.ExecuteScalar(strSql, param);
            return count > 0;
        }

        public async Task<bool> ExistsAsync(string where, object param = null)
        {
            string strSql = $"SELECT TOP 1 1 FROM Product {where}";
            var count = await _context.ExecuteScalarAsync(strSql, param);
            return count > 0;
        }

        public Product FirstOrDefault(string where, object param = null)
        {
            string strSql = $"SELECT TOP 1 * FROM Product {where}";
            return _context.QueryFirstOrDefault<Product>(strSql, param);
        }

        public async Task<Product> FirstOrDefaultAsync(string where, object param = null)
        {
            string strSql = $"SELECT TOP 1 * FROM Product {where}";
            return await _context.QueryFirstOrDefaultAsync<Product>(strSql, param);
        }

        public T FirstOrDefault<T>(string sql, object param = null)
        {
            return _context.QueryFirstOrDefault<T>(sql, param);
        }

        public async Task<T> FirstOrDefaultAsync<T>(string sql, object param = null)
        {
            return await _context.QueryFirstOrDefaultAsync<T>(sql, param);
        }

        public IEnumerable<Product> Fetch(SqlBuilder where)
        {
            var strSql = where.AddTemplate(@"SELECT * FROM Product /**where**/ /**orderby**/");
            return _context.Query<Product>(strSql.RawSql, strSql.Parameters);
        }

        public async Task<IEnumerable<Product>> FetchAsync(SqlBuilder where)
        {
            var strSql = where.AddTemplate(@"SELECT * FROM Product /**where**/ /**orderby**/");
            return await _context.QueryAsync<Product>(strSql.RawSql, strSql.Parameters);
        }

        public IEnumerable<T> Fetch<T>(string sql, SqlBuilder where, bool orderBy = true)
        {
            var _sql = orderBy ? $"{sql} /**where**/ /**orderby**/" : $"{sql} /**where**/";
            var strSql = where.AddTemplate(_sql);
            return _context.Query<T>(strSql.RawSql, strSql.Parameters);
        }

        public async Task<IEnumerable<T>> FetchAsync<T>(string sql, SqlBuilder where, bool orderBy = true)
        {
            var _sql = orderBy ? $"{sql} /**where**/ /**orderby**/" : $"{sql} /**where**/";
            var strSql = where.AddTemplate(_sql);
            return await _context.QueryAsync<T>(strSql.RawSql, strSql.Parameters);
        }

        public async Task<Page<Product>> PageAsync(long pageIndex, long pageSize, SqlBuilder builder)
        {
            var strSql = "SELECT * FROM Product";
            return await PageAsync<Product>(strSql, pageIndex, pageSize, builder);
        }

        public async Task<Page<T>> PageAsync<T>(string sql, long pageIndex, long pageSize, SqlBuilder builder)
        {
            var strSql = builder.AddTemplate($"{sql} /**where**/ /**orderby**/");
            return await _context.PageAsync<T>(pageIndex, pageSize, strSql.RawSql, strSql.Parameters);
        }

        public async Task<SqlMapper.GridReader> QueryMultipleAsync(string sql, object param = null)
        {
            return await _context.QueryMultipleAsync(sql, param);
        }
    }
    #endregion
```

根据自身需要进行调整或扩展，一般借助 T4 模板生成

### 数据库连接

通过 Ioptions 模式读取配置文件 appsettings 中连接字符串

```C#
public class MyDBContext : DapperDBContext
    {
        public MyDBContext(IOptions<DapperDBContextOptions> optionsAccessor) : base(optionsAccessor)
        {
        }

        protected override IDbConnection CreateConnection(string connectionString)
        {
            IDbConnection conn = new SqlConnection(connectionString);
            return conn;
        }
    }
```

## 四、Dapper 使用

Startup.cs 注入并读取数据库连接字符串

```json
{
  "SQLConnString": "Data Source=(local);Initial Catalog=database;Persist Security Info=True;User ID=sa;Password=123456;MultipleActiveResultSets=True;",
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

```C#
services.AddDapperDBContext<MyDBContext>(options =>
            {
                options.Configuration = Configuration["SQLConnString"];
            });
```

简单示例 WebAPI 或 Net Core MVC 下的调用示例：

```C#
public class ProductController : BaseController
{
    private readonly IProductRepository _productRepository;


    public ProductController(
        IProductRepository productRepository

        )
    {

        _productRepository = productRepository;

    }

    //商品列表
    [HttpGet]
    public async Task<IActionResult> ProductList(DateTime? startDate, DateTime? endDate, int id = 1, int productStatus = 0, string keyword = "")
    {
        var model = new ProductModels();
        var builder = new Dapper.SqlBuilder();
        builder.Where("ProductStatus!=@ProductStatus", new { ProductStatus = productStatus });

        if (startDate.HasValue)
        {
            builder.Where("CreateTime>=@startDate", new { startDate = startDate.Value});
        }
        if (endDate.HasValue)
        {
            builder.Where("CreateTime<@endDate", new { endDate = endDate.Value.AddDays(1)});
        }

        if (!string.IsNullOrWhiteSpace(keyword))
        {
            builder.Where("Name LIKE @keyword", new { keyword = $"%{StringHelper.ReplaceSql(keyword)}%" });
        }

        builder.OrderBy("SortNum DESC,CreateTime DESC");

        var list = await _productRepository.PageAsync(id, PageSize, builder);


        model.ProductList = new PagedList<Product>(list.Items, id, PageSize, list.TotalItems);

        if (Request.IsAjaxRequest())
            return PartialView("_ProductList", model.ProductList);

        return View(model);
    }

    //添加商品
    [HttpPost]
    public async Task<int> AddProduct(ProductModels model)
    {
        return await _productRepository.InsertAsync(model);
    }


}
```

```C#
public partial interface IProductService
    {
        Task<bool> AddProduct(Product productInfo, List<ProductStock> skuList);

    }
    public class ProductService: IProductService
    {
        private readonly DapperDBContext _context;
        private readonly IUnitOfWorkFactory _uowFactory;

        public ProductService(DapperDBContext context, IUnitOfWorkFactory uowFactory)
        {
            _context = context;
            _uowFactory = uowFactory;
        }

        /// <summary>
        /// 添加商品
        /// </summary>
        /// <param name="productInfo"></param>
        /// <param name="skuList"></param>
        /// <returns></returns>
        public async Task<bool> AddProduct(Product productInfo, List<ProductStock> skuList)
        {
            var result = false;
            using (var uow = _uowFactory.Create())
            {
                //添加产品
                await _context.InsertAsync(productInfo);

                //添加Sku库存售价

                //await _context.InsertAsync(skuList);

                uow.SaveChanges();
                result = true;
            }
            return result;
        }

    }
```
