# Verifying the Container

A common mistake we make is forgetting to register a type with the container. Another is having a singleton depend on a
transient (or other lesser scoped) registration. xPike Extensions solves this by providing the cabability to verify
your `IServiceProvider` instance at startup. This ensures that:

* All root objects and their dependencies can be resolved.
* Singletons only depend on other singletons.
* Scoped registrations only depend on singletons and other scoped registrations.

Verifying the container configuration at startup can signifcantly reduce production issues caused by missing or improper
registrations not identified during testing.

## Step 1

Install the `XPike.Extensions.DependencyInjection` package:

```
dotnet add package XPike.Extensions.DependencyInjection
```

or

```
Install-Package XPike.Extensions.DependencyInjection
```

## Step 2

Call the extension method on `IServiceCollection` to add the verification functionality to the provider:

```cs
using Xpike.Extensions.DependencyInjection;
...
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    ...
    services.AddServiceProviderVerification();
}
```

## Step 3

Call the extension method on `IServiceProvider` to add the verify the container:

```cs
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.ApplicationServices.Verify(); //this should be the first thing you do.
    ...
    app.UseMvc();
}
```

## Performance Considerations

There is a performance hit at startup. The impact depends on the number of registrations.
You may want to only verify the container in non-production environments. Assuming you have your
`ASPNETCORE_ENVIRONMENT` set properly for each environment, you can simply...

```cs
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (!env.IsProduction())
        app.ApplicationServices.Verify();

    ...
    app.UseMvc();
}
```
