Twig Templating
===============

Adding support for the [Twig](http://twig.sensiolabs.org) templating engine is straightforward.

First, add the Nice Twig and Nice Templating extensions to your project. The Twig extension
depends on Nice Templating, so both extensions must be installed and loaded. You can accomplish
this by updating your `composer.json` or running `composer require` at the command line.

*   Example `composer.json`:

    ```json
    {
        "require": {
            "nice/framework": "~1.0",
            "nice/templating": "~1.0",
            "nice/twig": "2.0.x-dev"
        }
    }
    ```
    
    Then run `composer update` at the command line.
    

*   Using the `composer` command line tool, after Nice itself is installed:

    ```
    composer require nice/templating:~1.0 nice/twig:2.0.x-dev
    ```

> **Warning:** Version 2.0 of the extension is currently under development. Take care to note any BC breaks when updating.


With the extensions installed, modify your front controller:

```php
<?php

use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;
use Nice\Application;
use Nice\Router\RouteCollector;
use Nice\Extension\TemplatingExtension;
use Nice\Extension\TwigExtension;

require __DIR__ . '/../vendor/autoload.php';

$app = new Application();

$app->appendExtension(new TemplatingExtension());
$app->appendExtension(new TwigExtension());

$app->set('routes', function (RouteCollector $r) {
    $r->map('/hello/{name}', null, function (Application $app, Request $request, $name) {
        // Use the Templating service to render templates
        $rendered = $app->get('templating')->render('index.html.twig', array(
            'name' => $name
        ));
        
        return new Response($rendered);
    });
});

// Run the application
$app->run();
```

Once the TemplatingExtension is registered with your Nice application, the following service will be available:

* `templating` is your entry point to any templating engine. Use this service to render templates.

  This service will automatically know to use Twig or another templating language depending on what file extension
  the template has. Twig expects templates to end in `.twig`.

The parameter `twig.template_dir` is also made available. Its value will be whatever was passed into
the `template_dir` option in the constructor of your `TwigExtension` instance.


Generating URLs
---------------

The Nice Twig extension includes integration with Nice's router, exposing the following useful functions:

*   `path` Generates a URL. It takes a name and optionally, an array of parameters.

    For example:
    ```twig
    <a href="{{ path('user_edit', { id: 1 }) }}">Edit User</a>
    ```
    
*   `url` Generates an absolute URL. It takes a name and optionally, an array of parameters.

*   `asset` Generates a URL to the given asset. It takes a relative path to the asset, from the webroot.

    For example:
    ```twig
    <link href="{{ asset('css/styles.css') }}" rel="stylesheet">
    ```
