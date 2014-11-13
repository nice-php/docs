Configuring the Cache
=====================

> **Warning:** Caching is subject to change before 1.0 stable is released.

Passing `false` as the third parameter of `Nice\Application` constructor will disable
caching. The cache directory will be null if caching is disabled, which can be checked
in your own code.

```php
<?php

use Nice\Application;

// ...

$app = new Application('prod', true, false);

// Caching is disabled; the Cache Directory is null.
assert($app->getCacheDir() === null);

// or by calling isCacheEnabled
assert($app->isCacheEnabled() === false);
```