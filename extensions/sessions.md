Using the Session extension
===========================

By default, session management is disabled. If you'd like to enable it, add the following:

```php
<?php

use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\HttpFoundation\Request;
use Nice\Application;
use Nice\Router\RouteCollector;
use Nice\Extension\SessionExtension;

// ...

$app = new Application();
$app->appendExtension(new SessionExtension());

// ...

$app->run();
```

This extension exposes the following service:

* `session` will be an instance of 
[Symfony\Component\HttpFoundation\Session](http://api.symfony.com/2.5/Symfony/Component/HttpFoundation/Session/Session.html)

Additionally, the Session object is injected into the master Request object.

An example using the Session object:

```php
<?php

use Nice\Application;
use Nice\Extension\SessionExtension;

$app = new Application();
$app->appendExtension(new SessionExtension());

$app->set('routes', function (RouteCollector $r) {
    $r->map('/last-visit', null, function (Request $request) {
        $session = $request->getSession();
        
        $lastVisit = $session->get('last-visited', 'Never');
        $session->set('last-visited', date('Y-m-d'));        

        return new Response('You last visited on: ' . $lastVisit);
    });
});
```
