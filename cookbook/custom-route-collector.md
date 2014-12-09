Custom Route Collector
======================

In more complex setups, it can be desirable to move the route definitions from `web/index.php` to a 
dedicated file. This is done in Nice by implementing a custom RouteCollector, and registering it with
the dependency injection container.

> **Note:** This guide builds on the [Custom Extensions](custom-extensions.md) walk-through.

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

>   **Tip:** The above example
    [specifies class-based controllers](../getting-started/routing.md#using-classes-as-controllers)
    instead of the usual Closure actions.


Overriding the default collector
--------------------------------

A few boilerplate steps need to occur to complete our goal. We must override
the default `router.collector` service with our new implementation. We'll do this by creating a
custom dependency injection Extension.

```php
<?php

namespace Acme\Extension;

use Symfony\Component\DependencyInjection\Extension\Extension;
use Symfony\Component\DependencyInjection\Reference;

class AcmeExtension extends Extension
{
    public function load(array $configs, ContainerBuilder $container)
    {
        // Register your custom classes
        $container->register('router.collector', 'Acme\Router\RouteCollector')
            ->addArgument(new Reference('router.parser'))
            ->addArgument(new Reference('router.data_generator'));
    }
}
```

Finally, append your new extension in your front controller:

```php
<?php

use Symfony\Component\HttpFoundation\Response;
use Nice\Application;
use Acme\Extension\AcmeExtension;

require __DIR__ . '/../vendor/autoload.php';

$app = new Application();
$app->appendExtension(new AcmeExtension());

$app->run();
```

