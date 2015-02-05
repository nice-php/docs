Symfony 2 PHP Templating
========================

Nice builds its templating foundation on Symfony components, specifically the Templating component.

Out of the box, the Nice Templating extension supports the Symfony 2 PHP Templating language, with no
additional dependencies!

First, add the Nice Templating extension to your project. You can do this by updating your `composer.json` or
running `composer require` at the command line.

*   Example `composer.json`:

    ```json
    {
        "require": {
            "nice/framework": "~1.0",
            "nice/templating": "~1.0"
        }
    }
    ```
    
    Then run `composer update` at the command line.
    

*   Using the `composer` command line tool, after Nice itself is installed:

    ```
    composer require nice/templating:~1.0
    ```

With the extension installed, all that's needed is some modifications to your front controller:

```php
<?php

use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;
use Nice\Application;
use Nice\Router\RouteCollector;
use Nice\Extension\TemplatingExtension;

require __DIR__ . '/../vendor/autoload.php';

$app = new Application();

// Register the Templating extension
$app->appendExtension(new TemplatingExtension(array(
    // We must explicitly turn on the Symfony 2 PHP templating engine
    'enable_php_engine' => true
)));

$app->set('routes', function (RouteCollector $r) {
    $r->map('/hello/{name}', null, function (Application $app, Request $request, $name) {
        // Use the Templating service to render templates
        $rendered = $app->get('templating')->render('index.html.php', array(
            'name' => $name
        ));
        
        return new Response($rendered);
    });
});

// Run the application
$app->run();
```

> **Tip:** The default views directory is `%app.root_dir%/views`. You can change this by passing in the `template_dir`
  option to the TemplatingExtension constructor.

Once the TemplatingExtension is registered with your Nice application, the following service will be available:

* `templating` is your main access point to any templating engine you use with Nice. Use this service to render templates,
  regardless of the engine responsible. See [Twig](twig.md) or [Plates PHP](plates.md) integration for more details.

The parameter `php.template_dir` is also made available. Its value will be whatever was passed into
the `template_dir` option in the constructor of your `TemplatingExtension` instance.


Generating URLs
---------------

The Nice Templating extension includes integration with Nice's router, exposing the following useful helpers:

*   `$view['router']->getPath($name, $params)` Generates a URL. It takes a name and optionally, an array of parameters.

    For example:
    ```php
    <a href="<?php echo $view['router']->getPath('user_edit', array('id' => 1)); ?>">Edit User</a>
    ```
    
*   `$view['router']->getUrl($name, $params)` Generates an absolute URL. It takes a name and optionally, an array of parameters.

*   `$view['assets']->getPath($name)` Generates a URL to the given asset. It takes a relative path to the asset, from the webroot.

    For example:
    ```php
    <link href="<?php echo $view['assets']->getPath('css/style.css'); ?>" rel="stylesheet">
    ```

*   Additionally, the Nice Templating extension wires up the `$view['slots']` helper for you.
    [Check out the Symfony documentation](http://symfony.com/doc/current/components/templating/helpers/slotshelper.html)
    for more details.
