Custom Dependency Injection Extensions
======================================

The Symfony DependencyInjection component is very powerful, if not a little bit verbose. 
Nice allows the integration of custom DI extensions, which allow you to leverage the full power
of the Symfony service container in your application.

Custom Extensions allow you to register new services with the service container and override
built-in services with your own implementations.

Creating an Extension
---------------------

Create a file in your project called `DemoExtension.php`. By convention, this should go in a child
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

Back in your front controller, call `appendExtension` on your `Nice\Application` instance.

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

> **Tip:** The order of your extensions usually doesn't matter, but if you need to control the order, you can
  use `prependExtension` to prioritize an extension over other extensions.


Creating a new service definition
---------------------------------

For this example, assume you have an `Acme\Mailer` class. This class takes a single dependency in its constructor,
an `Acme\Mailer\DriverInterface`.

The `Mailer` class might look something like:

```php
<?php

namespace Acme;

use Acme\Mailer\DriverInterface;

class Mailer
{
    public function __construct(DriverInterface $driver)
    {
        // ...
    }

    // ...
}
```

And an implementation of `DriverInterface` might be:

```php
<?php

namespace Acme\Mailer;

class SendmailDriver implements DriverInterface
{
    // ...
}
```

Inside your extension's `load` method, you can leverage the full power of a `ContainerBuilder` to define
the relationship between our example classes.

```php
<?php

namespace Acme\Extension;

use Symfony\Component\DependencyInjection\Extension\Extension;
use Symfony\Component\DependencyInjection\Reference;

class DemoExtension extends Extension
{
    public function load(array $configs, ContainerBuilder $container)
    {
        // Register your custom classes
        $container->register('acme.mailer.driver', 'Acme\Mailer\SendmailDriver')
            ->setPublic(false);

        $container->register('acme.mailer', 'Acme\Mailer')
            ->addArgument(new Reference('acme.mailer.driver'));
    }
}
```

We defined a service called `acme.mailer.driver`. Since this service is not intended to be used outside of this
specific context, we've marked it as private by calling `setPublic(false)`.

Then, we defined another service called `acme.mailer`. This service will be an instance of `Acme\Mailer`
and will receive the configured `acme.mailer.driver` service as the first argument to its constructor.

Now you can use your `acme.mailer` service like any other.

```php
# Back in web/index.php...
// ...

$mailer = $app->get('acme.mailer');

// ...
```

>   **Info:** For a more complete reference, see the
    [DependencyInjection reference](http://symfony.com/doc/current/components/dependency_injection/definitions.html).


Creating a Configuration Class
------------------------------

Nice also makes use of the Symfony 2 Config component to expose configuration options. This can be used to
configure your application using a variety of mechanisms: in PHP, YAML, or XML.

### Getting started

Start by creating a `Configuration` class. Nice's naming convention is `LogExtension` and `LogConfiguration`.

```php
<?php

namespace Acme\Extension;

use Symfony\Component\Config\Definition\Builder\TreeBuilder;
use Symfony\Component\Config\Definition\ConfigurationInterface;

class AcmeConfiguration implements ConfigurationInterface
{
    /**
     * Generates the configuration tree builder.
     *
     * @return \Symfony\Component\Config\Definition\Builder\TreeBuilder The tree builder
     */
    public function getConfigTreeBuilder()
    {
        $treeBuilder = new TreeBuilder();
        $rootNode = $treeBuilder->root('acme');

        $rootNode
            ->children()
                ->scalarNode('my_config')->end()
            ->end();

        return $treeBuilder;
    }
}
```

> **Info:** See the (Symfony documentation)[http://symfony.com/doc/current/components/config/definition.html]
  for additional information on using a `TreeBuilder`.


### Modifying your extension

Next, you need to override the `getConfiguration` method in your Extension class.

```php
<?php

namespace Acme\Extension;

use Symfony\Component\DependencyInjection\ContainerBuilder;
use Symfony\Component\DependencyInjection\Extension\Extension;

class AcmeExtension extends Extension
{
    /**
     * Returns extension configuration
     *
     * @param array            $config    An array of configuration values
     * @param ContainerBuilder $container A ContainerBuilder instance
     *
     * @return AcmeConfiguration
     */
    public function getConfiguration(array $config, ContainerBuilder $container)
    {
        return new AcmeConfiguration();
    }
}
```

