Configuring the Security extension
==================================

A standalone security component is available for Nice that allows simple authentication.

> **Note:** The Security extension requires the Session extension. [See here](sessions.md) for information on 
configuring the Session extension.

To get started, add the Nice Security extension to your project. You can do this by updating your `composer.json` or
running `composer require` at the command line.

*   Example `composer.json`:

    ```json
    {
        "require": {
            "nice/framework": "1.0.x-dev",
            "nice/security": "1.0.x-dev"
        }
    }
    ```
    
    Then run `composer update` at the command line.
    

*   Using the `composer` command line tool

    ```
    composer require nice/security:1.0.x-dev
    ```


With the extension installed, register the Session and Security extensions with your Nice application.

```php
$app = new Application();
$app->appendExtension(new SessionExtension());
$app->appendExtension(new SecurityExtension(array(
    'firewall' => '.*',
    'authenticator' => array(
        'type' => 'username',
        'username' => 'someuser',
        'password' => 'a-really-strong-password'        
    )
)));
```

This configuration will require anyone visiting any page (other than the login related pages) to be authenticated.

If a user is not authenticated, they will be redirected to `/login`, by default. We must create a form at `/login` for
the user to login with.

```php
// Configure your routes
$app->set('routes', function (RouteCollector $r) {
    // ...

    $r->map('/login', 'login', function (Application $app, Request $request) {
        return new Response(
            '<form action="login_check" method="post">
                <input type="text" name="username" placeholder="Username" />
                <input type="password" name="password" placeholder="Password" />
                <button>Login</button>
            </form>'
        );
    });
});
```

Now visiting your app will redirect you to `/login`, where you can login with the configured credentials.


Using your own authenticator
----------------------------

The Security extension makes it simple to use your own authentication mechansim. Simply configure the extension to use
the `closure` type authenticator, and register a closure as your authenticator service. Your authenticator should return
`true` if the request should be authenticated, `false` otherwise.

```php
$app = new Application();
$app->appendExtension(new SessionExtension());
$app->appendExtension(new SecurityExtension(array(
    'firewall' => '.*',
    'authenticator' => array(
        'type' => 'closure'    
    )
)));

$app->set('security.authenticator', function (Request $request) {
    if ($request->get('token') === '123') {
        return true;
    }
    
    return false;
});
```


Checking if a user has failed to login
--------------------------------------

By default, an event subscriber adds an indicator to the session when a user fails to login. This can be used in
your own code to display an error to the user stating that their username or password is incorrect.

A contrived example of this usage:

```php
use Nice\Security\AuthenticationFailureSubscriber;

$app = new Application();

// ...

$app->set('routes', function (RouteCollector $r) {
    $r->map('/login', 'login', function (Request $request) {
        if (
            $request->getSession()->has(AuthenticationFailureSubscriber::AUTHENTICATION_ERROR)
        ) {
            return new Response("Sorry, you failed to login once. That's all we can allow.");
        }
        
        // ... carry on returning the regular login page
    });
});
```
