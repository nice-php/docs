Configuring the Log extension
===============================

Nice comes with built-in integration with [Monolog](https://github.com/Seldaek/monolog). This allows
for PSR-3 compliant logging in your application.


The first step is requiring Monolog in your project. You can do this by updating your `composer.json` or
running `composer require` at the command line.

*   Example `composer.json`:

    ```json
    {
        "require": {
            "nice/framework": "1.0.x-dev",
            "monolog/monolog": "~1.11"
        }
    }
    ```
    
    The run `composer update` at the command line.
    

*   Using the `composer` command line tool

    ```
    composer require monolog/monolog:~1.11
    ```

With Monolog is installed, the next step is registering the `LogExtension` with your Nice Application.

```php
use Nice\Extension\LogExtension;

// ...

$app = new Application();
$app->appendExtension(new LogExtension(array(
    'channels' => array(
        'default' => array(
            'handler' => 'stream',
            'level' => 100, // Debug
            'options' => array(
                'file' => '%app.log_dir%/dev.log'
            )
        )
    )
)));
```


With the above configuration, Nice will create the following service:

* `logger.default` is an instance of 
[Monolog\Logger](https://github.com/Seldaek/monolog/blob/master/src/Monolog/Logger.php), configured to write to the
specified log file on any messages with level DEBUG or higher.

> **Tip:** Check out [Monolog documentation](https://github.com/Seldaek/monolog#log-levels) for more information on
log levels and handlers.
