Advanced Usage
==============

Nice is designed to allow rapid creation of prototypes followed by a natural progression
to full-fledged applications. It accomplishes this by providing a default configuration
that can handle most scenarios, but offers complete customization and control.

*   [Create custom dependency injection extensions](custom-extensions.md) responsible 
    for configuring new services and parameters.
    
*   [Subclass `Nice\Application`](subclass-application.md) and override the `registerDefaultExtensions` 
    methods to keep your front controller clean of configuration.

*   [Create a custom route collector](custom-route-collector.md) to can leverage full router caching.
