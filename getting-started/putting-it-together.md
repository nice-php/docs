Putting it all together
=======================

For quick and dirty prototypes, you can get away with anonymous functions and using `$app` as a service locator,
but this method can quickly lead to a messy project.

There are a few key points to developing a maintainable Nice application.

* Avoid closure based routes.
* Register controllers as services.
* Create separate environments for production and development.


Best practices
--------------

Early on, using closures for your actions is fast and simple. However, it can be very
beneficial to refactor your controller actions into distinct classes and register them as
services. Implement a custom route collector to improve the readability and re-usability
of your route mapping. Avoid relying on having the service container available
to `get()` services; inject them properly instead.


Using nice/skeleton as a starting point
---------------------------------------

Coming soon.
