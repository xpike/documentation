# Introduction

xPike Settings provides application settings management for the xPike paved-road.

xPike Settings is the recommended source for application settings.

It uses xPike Configuration as its configuration source by default, and other providers can be added.
In addition, it adds a configurable layer of caching, as well as settings refresh capabilities.

Settings Providers are fully DI-managed objects (unlike Configuration Providers).

The use of Settings in xPike is analogous to IOptions in Asp.Net Core.

## Examples

Working examples can be found on [GitHub](https://github.com/xpike/settings/tree/master/examples).

## Usage

### Define your Settings POCO

```cs
using XPike.Settings;

namespace MyLibrary
{
    public class MySettings
    {
        public string CompanyName { get; set; }
        public IList<DateTime> UpcomingHolidays { get; set; }
        public IDictionary<string, string> LocationPhoneNumbers { get; set; }
    }
}
```

### Load Automatically from JSON

xPike supports a "touchless" settings system.

**Just inject `ISettings<MySettings>` wherever you want.**

xPike will search for a setting with a matching fully-qualified class name (eg `"MyLibrary.MySettings"`).
The settings will be deserialized from a JSON string.  **Note:** This may vary by Provider.

The value can come from any of the registered Configuration or Settings Providers.
xPike Settings will handle caching and periodic refresh automatically.

### Specifying Options

The built-in Settings Manager allows you to specify a custom configuration key.  You can also specify an action
to be called upon load to further customize your settings.

```cs
container.RegisterSingleton<ISettingsManager<MySettings>>(provider =>
    new SettingsManager(provider.ResolveDependency<ISettingsService>(),
        "myCustomKey",
        settings =>
        {
            // further customization of settings object

            return settings;
        });
```

### Custom Settings Manager

For the most flexibility, you can specify your own Settings Manager.

```cs
using XPike.Settings;

namespace MyLibrary
{
    public class MySettingsManager
        : SettingsManagerBase<MySettings>
    {
        protected override string ConfigurationKey => $"{nameof(MyLibrary)}::{nameof(MySettings)}";

        public MySettingsManager(ISettingsService settingsService)
            : base(settingsService)
        {
        }

        public override ISettings<TSettings> GetSettings()
        {
            var settings = new MySettings
            {
                CompanyName = GetValue("CompanyName"),
                UpcomingHolidays = GetValue<IList<DateTime>>("UpcomingHolidays"),
                LocationPhoneNumbers = string.Split(new[] {','},
                                                    GetValueOrDefault("LocationPhoneNumbers", "555-1212"))
            };

            return new Settings(ConfigurationKey, settings);
        }
    }
}
```

#### Registering your Custom Settings Manager

`container.RegisterSingleton<ISettingsManager<MySettings>, MySettingsManager>();`

**Note:** If you are using SimpleInjector for dependency injection, then you must be certain
that any custom Settings Managers are registered ***before the Settings Package***.  Since a
Package can load other Packages, this may require that you define them at the start of your
top-level Package.

#### Some things to note:

- The `GetValue()` and `GetValueOrDefault()` overloads on `SettingsManager<TSettings>` prepend the `ConfigurationKey` to the specified `key`.

- The default `ConfigurationKey` is the full class and namespace of the Settings POCO.

- In this example, the call to `GetValue<IList<DateTime>>(...)` loads its value from a JSON string in the `MyLibrary.MySettings::UpcomingHolidays` key.

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
