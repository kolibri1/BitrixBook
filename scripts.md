Подключение скриптов
====================

```php
use \Bitrix\Main\Page\Asset;

$Asset = Asset::getInstance();

//добавляем любую строку в <head></head>
$Asset->addString('<meta http-equiv="X-UA-Compatible" content="IE=edge">');

//Добавляем файл стилей
$Asset->addCss(SITE_TEMPLATE_PATH.'/styles/style.css');

//и файл скрипта
$Asset->addJs(SITE_TEMPLATE_PATH.'/js/script.js');
```

# Подкючение модулей
```php
use Bitrix\Main\Loader; 
//Подключаем модуль инфоблоков
Loader::includeModule("iblock"); 
```
