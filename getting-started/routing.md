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

For example, an action that should respond only to POST would pass: `['POST']` or `array('POST')` as the value:

```php
$app->set('routes', function (RouteCollector $r) {
    $r->map('/user/{id}/edit', 'user_edit', function (Application $app, $id) {
        // ... do something useful
    }, ['POST']);
});
```

> **Tip:** You may notice that you are able to map one action to multiple HTTP methods by passing an array like
  `['GET', 'POST']`. In general, this should be avoided as code should do one thing and one thing only;
  GET for fetching and POST for updating may share some code, but combining the two into a single action
  may be considered bad practice.

> See the Wikipedia article on the
  [single responsibility principle](http://en.wikipedia.org/wiki/Single_responsibility_principle) for a
  more thorough explanation.

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

> **Note:** The specified controller class is actually instantiated, and then the action method is called on that object.
  If you want to use static action methods, you must use the array callable syntax:
  `['UserController', 'staticAction']` or `array('UserController', 'staticAction')`.


### ContainerAware controllers

As an additional step, you may want access to the service container inside your controllers. Implement the
`Symfony\Component\DependencyInjection\ContainerAwareInterface` and when your controller is resolved and
instantiated, the service container will be injected using the controller's `setContainer` method.

> **Tip:** Injecting the service container itself is considered an anti-pattern, turning the container
  into a service locator rather than proper dependency injection. Consider using controllers as services instead.


Controllers as services
-----------------------

> **Notice:** Nice 1.1 introduced support for controllers as services. Make sure you've got the latest version
  to make use of this feature.

Providing your controller actions with the service container itself is a quick, easy way to get up and
running. For most apps, however, registering controllers as services is the preferred solution.

Register your route handler with a single colon, separating the name of the service and the name of the
action method. For example, `acme.some_controller:indexAction` will use the `acme.some_controller` service,
calling its `indexAction` method.

Reworking the previous example, we need to add a [custom dependency injection extension](../cookbook/custom-extensions.md).

```php
class AcmeExtension extends Extension
{
    public function load(array $configs, ContainerBuilder $container)
    {
        // Register our controller as a service, also configuring any dependencies.
        $container->register('acme.user_controller', 'UserController')
            ->addArgument(new Reference('doctrine.orm.entity_manager'));
    }
}
```

Back in your front controller, `web/index.php`:

```php
// ...

$app->appendExtension(new AcmeExtension());

$app->set('routes', function (RouteCollector $r) {
    $r->map('/user/{id}/edit', 'user_edit', 'acme.user_controller:editAction');
    $r->map('/user/{id}/update', 'user_update', 'acme.user_controller:updateAction');
});
```


Now what?
---------

Optimizing your app is important. Nice relies heavily on caching to stay performant.
[See here](caching.md) for details on customizing caching in your application.
