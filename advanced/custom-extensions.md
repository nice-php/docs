Custom Dependency Injection Extensions
======================================

The Symfony DependencyInjection component is very powerful, if not a little bit verbose. 
Nice allows the integration of custom DI extensions, which allow you to leverage the full power
of the Symfony ServiceContainer in your application.

Creating an Extension
---------------------

Create a file in your project called DemoExtension.php. By convention, this should go in a child 
namespace of your main project called `Extension` or `DependencyInjection`.

>   **Note:** You'll need to let Composer know about your project source files. Do this by modifying your
    [`composer.json`'s "autoload" section](https://getcomposer.org/doc/04-schema.md#autoload)

In your new Extension file, create a class called DemoExtension and subclass Symfony's DI Extension base class,
`Symfony\Component\DependencyInjection\Extension\Extension`.

```php
<?php

namespace Acme\Extension;

use Symfony\Component\DependencyInjection\Extension\Extension;

class DemoExtension extends Extension
{
    /**
     * Loads a specific configuration.
     *
     * @param array            $configs   An array of configuration values
     * @param ContainerBuilder $container A ContainerBuilder instance
     */
    public function load(array $configs, ContainerBuilder $container)
    {
        // Register your services here
    }
}
```

The `load` method receives a `ContainerBuilder` which is used to register custom services and add parameters.

>   Note: The `$configs` array can be used to allow configuration of services. See 
    [this section](#creating-a-configuration-class) for more information.

### Register your extension with your Nice application

Back in your front controller, called `appendExtension` or `prependExtension` on your `Nice\Application` instance.

```php
<?php

use Symfony\Component\HttpFoundation\Response;
use Nice\Application;
use Nice\Router\RouteCollector;
use Acme\Extension\DemoExtension;

require __DIR__ . '/../vendor/autoload.php';

$app = new Application();
$app->appendExtension(new DemoExtension());
```


Creating a new service definition
---------------------------------

Inside your extension's `load` method, you can leverage the full power of a `ContainerBuilder`.

```php
<?php

class DemoExtension extends Extension
{
    public function load(array $configs, ContainerBuilder $container)
    {
        // Register your custom classes
        $container->register('acme.mailer', 'Acme\Mailer')
            ->addArgument('sendmail');
    }
}
```

We defined a new service called `acme.mailer`. This service will be an instance of `Acme\Mailer` and will receive
"sendmail" as the first argument to its constructor.

>   **Info:** For a more complete reference, see the
    [DependencyInjection reference](http://symfony.com/doc/current/components/dependency_injection/definitions.html).


Creating a Configuration Class
------------------------------

Coming soon.
