Configuring Logging
===================

Nice comes with built-in Monolog integration.

> **Note:** For more information about installing and configuring the Log extension, see 
[relevant documentation](../extensions/log.md).

A Nice application always exposes its configured log directory. You can check this in your application:

```php
<?php

use Nice\Application;

// ...

$app = new Application('prod', true);

// The configured log directory is always available
echo $app->getLogDir();
```

Additionally, the following parameters are available:

* `app.log_dir` will be the path to the log directory.


> **Tip:** For further information on configuring logging, see the
[Log extension](../extensions/log.md) documentation.