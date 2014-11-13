Using the Session extension
===========================

By default, session management is disabled. If you'd like to enable it, add the following:

```php
<?php

use Nice\Application;
use Nice\Extension\SessionExtension;

// ...

$app = new Application();
$app->appendExtension(new SessionExtension());

// ...

$app->run();
```