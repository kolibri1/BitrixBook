#Подключение скриптов

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
Начиная с версии 15.5 для шаблона компонента доступно подключение подключение внешних скриптов и стилей(помимо стандартных(script.js и style.css):
```php
$this->addExternalCss("/local/styles.css");
$this->addExternalJS("/local/component.js");
```
особенность такого подключения в правильной работе с кешем.

# Подкючение модулей
```php
use Bitrix\Main\Loader; 
//Подключаем модуль инфоблоков
Loader::includeModule("iblock");

//Очистить кеш модуля
Loader::clearModuleCache("iblock")
```
