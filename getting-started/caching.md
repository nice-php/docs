Configuring the Cache
=====================

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

Additionally, the following parameters are available:

* `app.cache` will be `true` if caching should be enabled, `false` otherwise
* `app.cache_dir` will be the path to the cache directory, or `null` if caching should be disabled.


> **Tip:** For further information on actually using the cache, see the 
[Cache extension](../extensions/cache.md) documentation.
