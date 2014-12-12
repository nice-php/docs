Compiler Passes
===============

Compiler passes allow the manipulation of service definitions prior to compilation of the service container.

The most common use-cases for using compiler passes:

* Tagging services. This allow you to register those tagged services with another service.

  An example is automatically registering TwigExtension services with the Twig environment.

* Overriding default services. You can customize built-in services, for example, overriding the default
  RouteCollector service.


Getting started
---------------

Define your Compiler Pass. Implement the `process` method, modifying the ContainerBuilder as necessary.

```php
<?php

namespace Acme\DependencyInjection\Compiler;

use Symfony\Component\DependencyInjection\Compiler\CompilerPassInterface;
use Symfony\Component\DependencyInjection\ContainerBuilder;

class CustomCompilerPass implements CompilerPassInterface
{
    public function process(ContainerBuilder $container)
    {
        // ...
    }
}
```


Registering your compiler pass
------------------------------

A Nice application allows the registration of compiler passes through the `addCompilerPass` method.

Register your custom compiler pass with your application in `web/index.php`.

```php
<?php

use Nice\Application;
use Acme\DependencyInjection\Compiler\CustomCompilerPass;
// ...

$app = new Application();
$app->addCompilerPass(new CustomCompilerPass());
```

