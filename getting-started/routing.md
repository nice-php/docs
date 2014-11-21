Routing
=======

Nice uses FastRoute as a router, but also provides URL generation using named routes.

By default, routes are un-named. You can add named routes with `addNamedRoute` method.

Below is a slightly different example of the introduction application.

```php
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;
use Nice\Application;
use Nice\Router\RouteCollector;

$app = new Application();

// Configure your routes
$app->set('routes', function (RouteCollector $r) {
    $r->addNamedRoute('home', 'GET', '/', function (Application $app) {
        $url = $app->get('router.url_generator')->generate('hello', array('name' => 'Tyler'));
        
        return new Response('Hello, world. <a href="' . $url . '">Hello, Tyler.</a>');
    });
    
    $r->addNamedRoute('hello', 'GET', '/hello/{name}', function (Application $app, $name) {
        return new Response('Hello, ' . $name . '!');
    });
});
```

We used `addNamedRoute` and passed the name of the route as the first parameter. Inside the first action is an
example of using the URL generator to generate a route for the route named `hello`.

You can also generate absolute URLs by passing `true` as the third parameter.

The full `generateUrl` method signature is:

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