Plates PHP Templating
=====================

Nice also supports Plates PHP templating engine with the help of an extension.

> **Tip:** By default, both Plates and the Symfony 2 PHP Engine look for the extension `.php`. Both can be used,
  however, by passing in the `file_extension` option to the PlatesExtension constructor.

The Plates extension depends on the Nice Templating extension, so both must be installed and registered.

First, add both extensions to your project. You can do this by updating your `composer.json` or
running `composer require` at the command line.

*   Example `composer.json`:

    ```json
    {
        "require": {
            "nice/framework": "~1.0",
            "nice/templating": "1.0.x-dev",
            "nice/plates": "1.0.x-dev"
        }
    }
    ```
    
    Then run `composer update` at the command line.
    

*   Using the `composer` command line tool, after Nice itself is installed:

    ```
    composer require nice/templating:1.0.x-dev nice/plates:1.0.x-dev
    ```

> **Warning:** These extensions are currently under development. Take care to note any BC breaks when updating.


With the extensions installed, all that's needed is a little bit of wiring up. Back in your front controller, `index.php`:

```php
<?php

use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;
use Nice\Application;
use Nice\Router\RouteCollector;
use Nice\Extension\TemplatingExtension;
use Nice\Extension\PlatesExtension;

require __DIR__ . '/../vendor/autoload.php';

$app = new Application();

$app->appendExtension(new TemplatingExtension());
$app->appendExtension(new PlatesExtension());

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

> **Tip:** You can define where Plates looks for templates by passing in the `template_dir` option to the PlatesExtension
  constructor.

Once both the TemplatingExtension and PlatesExtension are registered with your Nice application, the following
service will be available:

* `templating` is a wrapper service to your templating engines. Use this service to render templates.

  This service will automatically know to use Plates or another templating language depending on what file extension
  the template has. By default, Plates looks for templates ending in `.php`.

The parameter `plates.template_dir` is also made available. Its value will be whatever was passed into
the `template_dir` option in the constructor of your `PlatesExtension` instance.

Generating URLs
---------------

Under development.
