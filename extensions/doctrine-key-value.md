Configuring Doctrine Key-Value Store
====================================

Nice features a basic [Doctrine Key-Value Store](https://github.com/doctrine/KeyValueStore) bridge that 
integrates the two, exposing a few useful services.

>   **Note:** Currently the only supported storage is the `DoctrineCacheStorage`. This means that the 
    [Cache extension](cache.md) must be configured for the Doctrine Key-Value Store to work correctly.

First, add the Nice Doctrine Key-Value extension to your project. You can do this by updating your `composer.json` or
running `composer require` at the command line.

*   Example `composer.json`:

    ```json
    {
        "require": {
            "nice/framework": "1.0.x-dev",
            "nice/doctrine-key-value": "1.0.x-dev",
            "doctrine/key-value-store": "dev-master",
        }
    }
    ```
    
    The run `composer update` at the command line.
    

*   Using the `composer` command line tool

    ```
    composer require nice/doctrine-key-value:1.0.x-dev doctrine/key-value-store:dev-master
    ```

Once Doctrine Key-Value Store and the Nice extension are installed, open your front controller (usually `web/index.php`) 
and add the following:

```php
use Nice\Extension\DoctrineKeyValueExtension;

// ...

$app = new Application();
$app->appendExtension(new DoctrineKeyValueExtension(array(
    'key_value' => array(
        'mapping' => array(
            'paths' => '%app.root_dir%/src',
        ),
    ),
)));
```

Two new services will now be available:

* `doctrine.key_value.entity_manager` is the EntityManager. It is an instance of 
[Doctrine\KeyValueStore\EntityManager](https://github.com/doctrine/KeyValueStore/blob/master/lib/Doctrine/KeyValueStore/EntityManager.php).
* `doctrine.key_value.configuration` is the Configuration instance for the entity manager.
