# AWS X-Ray SDK for Entity Framework Core 3.0 and above

![Screenshot of the AWS X-Ray console](images/example_servicemap.png?raw=true)

## Installing

For installing AWS X-Ray SDK for .NET and .NET Core, please take reference to [Link](https://github.com/aws/aws-xray-sdk-dotnet#aws-x-ray-sdk-for-net-and-net-core)

## Getting Help

Use the following community resources for getting help with the SDK. We use the GitHub issues for tracking bugs and feature requests.

* Ask a question in the [AWS X-Ray Forum](https://forums.aws.amazon.com/forum.jspa?forumID=241&start=0).
* Open a support ticket with [AWS Support](http://docs.aws.amazon.com/awssupport/latest/user/getting-started.html).
* If you think you may have found a bug, open an [issue](https://github.com/aws/aws-xray-sdk-dotnet/issues/new).

## Opening Issues

If you encounter a bug with the AWS X-Ray SDK for .NET/.NET Core, we want to hear about
it. Before opening a new issue, search the [existing issues](https://github.com/aws/aws-xray-sdk-dotnet/issues) to see if others are also experiencing the issue. Include platform (.NET/ .NET Core). 
In addition, include the repro case when appropriate.

The GitHub issues are intended for bug reports and feature requests. For help and questions about using the AWS X-Ray SDK for .NET and .NET Core, use the resources listed
in the [Getting Help](https://github.com/aws/aws-xray-sdk-dotnet#getting-help) section. Keeping the list of open issues lean helps us respond in a timely manner.

## Documentation

The [developer guide](https://docs.aws.amazon.com/xray/latest/devguide) provides in-depth guidance about using the AWS X-Ray service.
Following API reference documentation provides guidance for using the SDK and module-level documentation.
* The [API Reference for .NET](http://docs.aws.amazon.com/xray-sdk-for-dotnet/latest/reference/index.html)
* The [API Reference for .NET Core](http://docs.aws.amazon.com/xray-sdk-for-dotnetcore/latest/reference/index.html)
* AWS X-Ray SDK Documentation for [.NET SDK](https://docs.aws.amazon.com/xray/latest/devguide/xray-sdk-dotnet.html)
* [Sample Apps](https://github.com/aws-samples/aws-xray-dotnet-webapp)

## Entity Framework Core Support 

We support tracing sql request generated through Entity Framework Core platform using `EFInterceptor`. 

### ASP.NET Core on Entity Framework Core (.NET Core)

For how to start with Entity Framework Core in an ASP.NET Core web app, please take reference to [Link](https://docs.microsoft.com/en-us/aspnet/core/data/ef-rp/intro?view=aspnetcore-3.1&tabs=visual-studio)

Due to the fact that not all database provider support Entity Framework 3.0 and above, currently 
`EFInterceptor` can only trace sql queries towards: SqlServer, MySQL, SQLite, PostgreSQL and FireBird. Please make sure that you are using the Nuget package with a supportive provider and with a correct version (EF Core >= 3.0).

#### Setup

In order to trace sql queries, you can register `DBContext` with `EFInterceptor` in the `startup.cs` file. For instance, when dealing with MySql server using Nuget: [Pomelo.EntityFrameworkCore.MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql) 

```
public void ConfigureServices(IServiceCollection services){ 

services.AddDbContext<YourDbContext>(options => options.UseMySql(connectionString).AddInterceptors(new EFInterceptor()));

}
```

#### Capture SQL Query text in the traced SQL calls to SQL Server

You can also opt in to capture the `DBCommand.CommandText` as part of the subsegment created for your SQL query. The collected `DBCommand.CommandText` will appear as `sanitized_query` in the subsegment JSON. By default, this feature is disabled due to security reasons. 
If you want to enable this feature, it can be done in two ways. First, by setting the `CollectSqlQueries` to true in the global configuration for your application as follows:

##### For .Net Core (In `appsettings.json` file, configure items under XRay key)

```
{
  "XRay": {
    "CollectSqlQueries":"true"
  }
}
```

Secondly, you can set the `collectSqlQueries` parameter in the `EFInterceptor` instance as true to collect the SQL query text for SQL Server query calls made using this instance. If you set this parameter as false, it will disable the `CollectSqlQuery` feature for this `EFInterceptor` instance.

```
public void ConfigureServices(IServiceCollection services){

services.AddDbContext<YourDbContext>(options => options.UseMySql(connectionString).AddInterceptors(new EFInterceptor(true)));

}
```

NOTE:

1. You should not enable either of these properties if you are including sensitive information as clear text in your SQL queries.
2. Parameterized values will appear in their tokenized form and will not be expanded.
3. The value of `collectSqlQueries` in the `EFInterceptor` instance overrides the value set in the global configuration using the CollectSqlQueries property.

## License

The AWS X-Ray SDK for .NET and .NET Core is licensed under the Apache 2.0 License. See LICENSE and NOTICE.txt for more information.
