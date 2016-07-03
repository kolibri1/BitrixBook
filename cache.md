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
Тегированный кеш:
```php
//добавление тега
$cache_manager = Bitrix\Main\Application::getInstance()->getTaggedCache();
$cache_manager->startTagCache('/path_cache');
$cache_manager->registerTag('tag');
$cache_manager->endTagCache();

//очистить по тегу
$cache_manager->clearByTag('tag');
```
