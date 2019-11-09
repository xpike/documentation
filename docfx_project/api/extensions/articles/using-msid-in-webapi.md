# Using Microsoft.Extensions.DependencyInjection in Asp.Net WebAPI

The XPike.Extensions.DependencyInjection.WebApi package provides integration of Microsoft.Extensions.DependencyInjection
into legacy Asp.Net WebAPI application.

## Step 1

Install the `XPike.Extensions.DependencyInjection.WebApi` package:

```
dotnet add package XPike.Extensions.DependencyInjection.WebApi
```

or

```
Install-Package XPike.Extensions.DependencyInjection.WebApi
```

## Step 2

Create an instance of `Microsoft.Extensions.DependencyInjection.ServiceCollection` and register your application's
services.

```cs
IServiceCollection services = new ServiceCollection();
services.AddServiceProviderVerification();

services.AddSingleton<ILogger, Logger>();

```

## Step 3

Add the WebAPI controllers to the services collection. You can add them manually or use the extension method provided
in XPike.Extensions.DependencyInjection.WebApi`. The advantage of the extension method is it looks at every assembly in
the AppDomain for an ApiController and adds it to the service collection with a scoped lifetime for you. The method does
use reflection to accomplish this. Depending on the number of assemblies and controllers in your application, you may
want to consider adding your controllers explicitly in increase startup performance.

```cs
services.AddApiControllers();
```

## Step 4

Build and optionally verify the service provider. See [Verifying the Container](verifying-the-container.md) for more
information on verifying the container and performance considerations when doing so.

```cs
IServiceProvider provider = services.BuildServiceProvider();
provider.Verify();
```

## Step 5

Lastly, tell WebAPI to use Microsoft.Extensions.DependencyInjection to resolve controllers and their dependencies. This is done
by using the `MicrosoftDependencyResolver` found in `XPike.Extensions.DependencyInjection.WebApi`.

```cs
GlobalConfiguration.Configuration.DependencyResolver = new MicrosoftDependencyResolver(provider);
```

## Completed Application_Start Method

Global.asax.cs

```cs
using Microsoft.Extensions.DependencyInjection;
using XPike.Extensions.DependencyInjection.WebApi;
...

public class WebApiApplication : System.Web.HttpApplication
{
    protected void Application_Start()
    {
        GlobalConfiguration.Configure(WebApiConfig.Register);

        // Create a new service collection
        IServiceCollection services = new ServiceCollection();
        services.AddServiceProviderVerification();

        // Add your services
        services.AddSingleton<ILogger, Logger>();

        // Add the WebApi controllers to the collection
        services.AddApiControllers();

        // Create and verify the service provider
        IServiceProvider provider = services.BuildServiceProvider();
        provider.Verify();

        // Tell WebAPI it use Microsoft.Extensions.DependencyInjection
        // to resolve dependencies
        GlobalConfiguration.Configuration.DependencyResolver = 
            new MicrosoftDependencyResolver(provider);

        // Resolve and start any background services...
        ILogger logger = provider.GetService<ILogger>();
        logger.LogInfo("Service Started");
    }
}
```
