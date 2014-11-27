Custom Dependency Injection Extensions
======================================

The Symfony DependencyInjection component is very powerful, if not a little bit verbose. 
Nice allows the integration of custom DI extensions, which allow you to leverage the full power
of the Symfony ServiceContainer in your application.

Creating an Extension
---------------------

Create a file in your project called DemoExtension.php. By convention, this should go in a child 
namespace of your main project called `Extension` or `DependencyInjection`.

In this file, create a class called DemoExtension and subclass Symfony's DI Extension base class,
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
     *
     * @throws \InvalidArgumentException When provided tag is not defined in this extension
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


Creating a new service definition
---------------------------------

Coming soon.


Creating a Configuration Class
------------------------------

Coming soon.