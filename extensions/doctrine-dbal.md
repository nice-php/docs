Configuring Doctrine DBAL
=========================

Nice includes built-in support for Doctrine DBAL.


First, install the DBAL into your project:

```
php composer.phar require doctrine/dbal:~2.4
```

This command will add Doctrine DBAL 2.4 to your project. Once it is installed, open your `web/index.php` and 
add the following:

```php
use Nice\Extension\DoctrineDbalExtension;

// ...

$app = new Application();
$app->appendExtension(new DoctrineDbalExtension(array(
    'database' => array(
        'driver' => 'pdo_sqlite',
        'path' => '%app.root_dir%/sqlite.db'
    )
)));
```

Two new services will now be available:

* `doctrine.dbal.database_connection` is the connection to the database. It is an instance of 
[Doctrine\DBAL\Connection](http://doctrine-dbal.readthedocs.org/en/latest/reference/data-retrieval-and-manipulation.html).
* `doctrine.dbal.configuration` is the Configuration instance for the database.

Now, assuming you've created `sqlite.db` in your app's root directory, create the `messages` table:

```sql
CREATE TABLE messages
(
    id INTEGER PRIMARY KEY NOT NULL,
    message TEXT NOT NULL
);
INSERT INTO messages (1, "This is a message!");
```

Then, in your `web/index.php` where you define your routes, and add a new route:

```php
use Symfony\Component\HttpFoundation\JsonResponse;

// ...
// Configure your routes
$app->set('routes', function (RouteCollector $r) {
    // ...
    
    $r->addRoute('GET', '/messages', function (Application $app, Request $request) {
        $conn = $app->get('doctrine.dbal.database_connection');
        $results = $conn->executeQuery("SELECT * FROM messages")->fetchAll();

        return new JsonResponse($results);
    }); 
});
```

Visiting your site at `index.php/messages` should result in the following JSON output:

```json
[{"id":"1","message":"This is a message!"}]
```
