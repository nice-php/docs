Routing
=======

Nice uses FastRoute as a router, but also provides URL generation using named routes.

Using the `map` method on `RouteCollector`, you may choose to pass in a name as the second parameter. You can also 
pass in `null` to leave the route un-named, as we saw in the [original example](your-first-app.md).

Below is a slightly different example of the introduction application.

```php
use Symfony\Component\HttpFoundation\Response;
use Nice\Application;
use Nice\Router\RouteCollector;

$app = new Application();

// Configure your routes
$app->set('routes', function (RouteCollector $r) {
    $r->map('/', 'home', function (Application $app) {
        $url = $app->get('router.url_generator')->generate('hello', array('name' => 'Tyler'));
        
        return new Response('Hello, world. <a href="' . $url . '">Hello, Tyler.</a>');
    });
    
    $r->map('/hello/{name}', 'hello', function (Application $app, $name) {
        return new Response('Hello, ' . $name . '!');
    });
});
```

We used `map` and passed the name of the route as the second parameter. Inside the first action is an
example of using the URL generator to generate a route for the route named `hello`.

You can also generate absolute URLs by passing `true` as the third parameter.

The full `generate` method signature is:

```php
interface UrlGeneratorInterface
{
    /**
     * Generate a URL for the given route
     *
     * @param string $name       The name of the route to generate a url for
     * @param array  $parameters Parameters to pass to the route
     * @param bool   $absolute   If true, the generated route should be absolute
     *
     * @return string
     */
    public function generate($name, array $parameters = array(), $absolute = false);
}
```

Specifying HTTP method
----------------------

The fourth parameter in `map` defines which HTTP methods the action should respond to. It takes either a string or
an array of strings. By default, routes will listen to GET method requests only.

For example, an action that listens to both GET and POST would pass: `[ 'GET', 'POST' ]` as the value:

```php
$app->set('routes', function (RouteCollector $r) {
    $r->map('/user/{id}/edit', 'user_edit', function (Application $app, $id) {
        // ... do something useful
    }, [ 'GET', 'POST' ]);
});
```


Using classes as controllers
----------------------------

You may want to segregate actions into controller classes rather than having a mess of Closures in your
front controller. By default, Nice allows the alternate syntax defining a class and action method:

```php
$app->set('routes', function (RouteCollector $r) {
    $r->map('/user/{id}/edit', 'user_edit', 'UserController::editAction');
    $r->map('/user/{id}/update', 'user_update', 'UserController::updateAction');
});
```

> **Note:** The specified controller class is actually instantiated, and then the action called on that object.
  If you want to use static action methods, you must use the array callable syntax:
  `[ 'UserController', 'staticAction' ]`


### ContainerAware controllers

As an additional step, you may want access to the service container inside your controllers. Implement the
`Symfony\Component\DependencyInjection\ContainerAwareInterface` and when your controller is resolved and
instantiated, the service container will be injected using the controller's `setContainer` method.
