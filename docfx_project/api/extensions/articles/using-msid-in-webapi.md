# Using Microsoft.Extensions.DependencyInjection in Asp.Net WebAPI

Global.asax.cs

```cs
public class WebApiApplication : System.Web.HttpApplication
{
    protected void Application_Start()
    {
        GlobalConfiguration.Configure(WebApiConfig.Register);

        // Create a new service collection
        IServiceCollection services = new ServiceCollection();

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
