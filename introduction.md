Nice, a PHP microframework
==========================

[![Build Status](http://img.shields.io/travis/nice-php/framework.svg?style=flat-square)](https://travis-ci.org/nice-php/framework)
[![Coverage](http://img.shields.io/codeclimate/coverage/github/nice-php/framework.svg?style=flat-square)](https://codeclimate.com/github/nice-php/framework)
[![Code Climate](http://img.shields.io/codeclimate/github/nice-php/framework.svg?style=flat-square)](https://codeclimate.com/github/nice-php/framework)
[![Latest Release](http://img.shields.io/packagist/v/nice/framework.svg?style=flat-square)](https://packagist.org/packages/nice/framework)

Nice is a simple microframework for PHP 5.4 and later. Nice integrates nikic's 
[FastRoute](https://github.com/nikic/FastRoute) router with 
the [Symfony2 HttpKernel](https://github.com/symfony/HttpKernel) and 
[Dependency Injection](https://github.com/symfony/DependencyInjection) components.

```php
<?php

use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;
use Nice\Application;
use Nice\Router\RouteCollector;

require __DIR__ . '/../vendor/autoload.php';

$app = new Application();
$app->set('routes', function (RouteCollector $r) {
    $r->map('/', 'home', function (Request $request) {
        return new Response('Hello, world');
    });
});
$app->run();
```

### Extensions available

Nice is built around dependency injection extensions used by the Symfony 2 DI container. Each extension
provides new services for your application, with powerful configuration options.

Nice comes with built-in support for [session management](extensions/sessions.md)
and logging utilizing [Monolog](extensions/log.md).

There are many more extensions to help ease the prototyping process, including:

* [Simple authentication](extensions/security.md)
* [Twig integration](templating/twig.md)
* [Plates PHP integration](templating/plates.md)
* [Doctrine DBAL](extensions/doctrine-dbal.md)
* [Doctrine ORM](extensions/doctrine-orm.md)
* [Doctrine Cache](extensions/cache.md)













