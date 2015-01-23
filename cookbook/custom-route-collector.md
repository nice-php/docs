Custom Route Collector
======================

In more complex setups, it can be desirable to move the route definitions from `web/index.php` to a 
dedicated file. This is done in Nice by implementing a custom RouteCollector, and registering it with
the dependency injection container.

> **Note:** This guide builds on the [Compiler Passes](compiler-passes.md) walk-through.

---------------

> **Warning:** A RouteCollector that maps any Closures as handlers cannot, at present, be cached.
  Use [class-based controller](../getting-started/routing.md#using-classes-as-controllers) handlers to
  allow file-based caching of routing data.

Getting started
---------------

To start out, create a new file called `RouteCollector.php` within your project's `Router` namespace. Continuing
the Acme example:

```php
<?php

namespace Acme\Router;

use Nice\Router\RouteCollector as BaseCollector;

class RouteCollector extends BaseCollector
{
    /**
     * Map all routes
     */
    protected function collectRoutes()
    {
        $this->map('/', 'home', 'Acme\Controller\DefaultController::indexAction');
        $this->map('/login', 'login', 'Acme\Controller\DefaultController::loginAction');
    }
}
```

>   **Tip:** The above example
    [specifies class-based controllers](../getting-started/routing.md#using-classes-as-controllers)
    instead of the usual Closure actions.


Overriding the default collector
--------------------------------

A few boilerplate steps need to occur to complete our goal. We must override
the default `router.collector` service with our new implementation. We'll do this by creating a
custom dependency injection CompilerPass.

```php
<?php

namespace Acme\DependencyInjection\Compiler;

use Symfony\Component\DependencyInjection\Compiler\CompilerPassInterface;
use Symfony\Component\DependencyInjection\ContainerBuilder;
use Symfony\Component\DependencyInjection\Reference;

class OverrideRouteCollectorPass implements CompilerPassInterface
{
    public function process(ContainerBuilder $container)
    {
        $container->getDefinition('router.collector')
            ->setClass('Acme\Router\RouteCollector')
            ->setArguments(array(
                new Reference('router.parser'),
                new Reference('router.data_generator')
            ));
    }
}
```

Finally, add your new compiler pass in your front controller:

```php
<?php

use Nice\Application;
use Acme\DependencyInjection\Compiler\OverrideRouteCollectorPass;

// ...

$app = new Application();
$app->addCompilerPass(new OverrideRouteCollectorPass());

$app->run();
```

