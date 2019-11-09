# Introduction

xPike Configuration provides configuration abstractions and default implementations for the xPike
paved-road.

XPike Configuration is intended for use before Dependency Injection is configured, to provide some basic bootstrap settings.
Because of this, ***Configuration Providers should not require injected dependencies.***

Providers are wrapped by the **Configuration Service** which scans them in reverse order, returning the first value it finds.
While Providers can be used directly, the Configuration Service should be your go-to (and it is compatible with .NET Core `IConfiguration`).

Though the Configuration Service does not rely on DI, it is intended to be registered with the container along with any Providers.
This is because XPike Settings (which adds capabilities such as caching and refresh - similar to .NET Core `IOptions`)
relies on the Configuration Service as its default Settings Provider.

For application-oriented settings, it is recommended to use the `XPike.Settings` Package to retrieve a settings POCO.

## Examples

Working examples can be found on [GitHub](https://github.com/xpike/configuration/tree/master/examples).

## Packages

### XPike.Configuration

[![Nuget](https://img.shields.io/nuget/v/XPike.Configuration)](https://nuget.org/packages/XPike.Configuration)

Abstractions used by the rest of xPike. This package also contains the configuration providers:

- EnvironmentConfigurationProvider: Storing/retrieving configuration using environment variables.
- NullConfigurationProvider: A Configuration Provider that yields no values. Primarily intended for use in unit/integration tests.

### Xpike.Configuration.AWS

[![Nuget](https://img.shields.io/nuget/v/Xpike.Configuration.AWS)](https://nuget.org/packages/Xpike.Configuration.AWS)

Support for storing/retrieving configuration using AWS Simple Parameter Store and AWS Secrets Manager.

### XPike.Configuration.Azure

[![Nuget](https://img.shields.io/nuget/v/XPike.Configuration.Azure)](https://nuget.org/packages/XPike.Configuration.Azure)

Support for storing/retrieving configuration using Azure Application Configuration Service.

> [!Important]
> Azure Application Configuration Service is in preview and Microsoft seems to be publishing breaking changes with each
> release. We don't recommend using this in production. We have provided support for experimentation and readiness for
> when Microsoft releases GA.

### Xpike.Configuration.Microsoft

[![Nuget](https://img.shields.io/nuget/v/Xpike.Configuration.Microsoft)](https://nuget.org/packages/Xpike.Configuration.Microsoft)

Provides configuration values to XPike by delegating lookups to the Microsoft.Extensions.Configuration.IConfiguration service.

### Xpike.Configuration.Microsoft.AspNetCore

[![Nuget](https://img.shields.io/nuget/v/Xpike.Configuration.Microsoft.AspNetCore)](https://nuget.org/packages/Xpike.Configuration.Microsoft.AspNetCore)

The inverse of `Xpike.Configuration.Microsoft`. This packages allows Microsoft.Extensions.Configuration.IConfiguration
to use XPike.Configuration as a source.

### Xpike.Configuration.System

[![Nuget](https://img.shields.io/nuget/v/Xpike.Configuration.System)](https://nuget.org/packages/Xpike.Configuration.System)

Support for storing/retrieving configuration using System.Configuration--i.e. from App.Config and Web.Config.
