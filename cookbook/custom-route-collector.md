Custom Route Collector
======================

In more complex setups, it can be desirable to move the route definitions from `web/index.php` to a 
dedicated file. This is done in Nice by implementing a custom RouteCollector, and registering it with
the dependency injection container.

> **Note:** This guide builds on the [Custom Extensions](custom-extensions.md) walk-through.

Getting started
---------------

To accomplish our goal, a few boilerplate steps need to occur.
