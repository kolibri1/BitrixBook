Кеширование
===========

```php
use \Bitrix\Main\Data\Cache;
$cache = Cache::createInstance();

if ($сache->initCache($cacheTime, $cacheId, $cacheDir))
{
    $result = $сache->getVars();
}
elseif ($сache->startDataCache())
{
    $result = array();
    // ...
    if ($isInvalid)
    {
        $cache->abortDataCache();
    }
    // ...
    $сache->endDataCache($result);
} 
```
