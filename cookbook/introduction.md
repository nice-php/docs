Cookbook
========

Nice is designed to allow rapid creation of prototypes followed by a natural progression
to full-fledged applications. It accomplishes this by providing a default configuration
that can handle most scenarios, but offers complete customization and control.

*   Create [custom dependency injection extensions](custom-extensions.md) responsible
    for configuring new services and parameters.

*   Create a [Configuration](custom-extensions.md#creating-a-configuration-class) for your
    custom extensions.

*   Load application configuration from YAML, XML, or PHP files or any other source with a
    [ConfigurationProvider](custom-extensions.md#loading-configuration-from-a-file).
    
*   Create [CompilerPasses](compiler-passes.md) to modify and extend the ServiceContainer
    after all Extension configurations have been applied.

*   Create a [custom route collector](custom-route-collector.md) to leverage full router caching.

*   [Utilize `ConfigCache`](config-cache.md) to cache generated data at time of deployment.
