Конвертация сайта из windows-1251 в кодировку utf-8
==============================
ВНИМАНИЕ: Если у вас демонстрационная версия битрикса, этот способ не подойдет!

На сервере должны быть следующие настройки для PHP:
```php
mbstring.func_overload 2
mbstring.internal_encoding UTF-8
```

Сначала конвертируем все файлы, включая ядро(php, html, js и т.п.) в кодировку UTF8. Обязательно без сохранения BOM!
Можно воспользоваться скриптом [convert_utf8.php](http://www.1c-bitrix.ru/download/files/scripts/convert_utf8.php).

В файле /bitrix/php_interface/dbconn.php добавить:
```php
define("BX_UTF", true);
```

В файле /bitrix/php_interface/after_connect.php должно быть:
```php
$DB->Query("SET NAMES 'utf8'");
$DB->Query("SET collation_connection = 'utf8_unicode_ci'");
```

В файле настроек /bitrix/.settings.php:
```php
'utf_mode' => 
  array (
    'value' => true,
    'readonly' => true,
  ),
```

В файле /bitrix/php_interface/after_connect_d7.php должно быть:
```php
$connection = \Bitrix\Main\Application::getConnection();
$connection->queryExecute("SET NAMES 'utf8'");
$connection->queryExecute('SET collation_connection = "utf8_unicode_ci"');
```


Для конвертации базы, выполнить запрос:
```sql
SELECT CONCAT(  'ALTER TABLE `', t.`TABLE_SCHEMA` ,  '`.`', t.`TABLE_NAME` ,  '` CONVERT TO CHARACTER SET utf8 COLLATE utf8_unicode_ci;' ) AS sqlcode
FROM  `information_schema`.`TABLES` t
WHERE 1
AND t.`TABLE_SCHEMA` =  'ИМЯ_КОНВЕРТИРУЕМОЙ_БАЗЫ'
ORDER BY 1
```
который сгенерирует список запросов для конвертации каждой таблицы, их необходимо выполнить

После конвертации базы данных, возможна проблема со свойствами типа html/text, из-за того, что в полях хранится сериализованный массив, и после конвертации длина значений не совпадает.

Для исправления можно выполнить подобный скрипт:
```php
<?
require($_SERVER["DOCUMENT_ROOT"]."/bitrix/header.php");

if($USER->IsAdmin())
{
    global $DB;
    $r = $DB->Query("SELECT ID FROM b_iblock_property WHERE PROPERTY_TYPE='S'");
    while($prop = $r->Fetch())
    {
        $rV = $DB->Query("SELECT ID,VALUE FROM b_iblock_element_property WHERE IBLOCK_PROPERTY_ID=".$prop['ID']." AND VALUE IS NOT NULL AND VALUE LIKE 'a:%'");
        while($pV = $rV->Fetch())
        {
            $s_cp1251 = iconv('utf-8','windows-1251',$pV['VALUE']);
            $u_cp1251 = unserialize($s_cp1251);
            array_walk_recursive($u_cp1251,'c');
            $s_utf8 = serialize($u_cp1251);
            $updateSql = "UPDATE b_iblock_element_property SET VALUE='".$DB->ForSql($s_utf8)."' WHERE ID=".$pV['ID'];
            $DB->Query($updateSql);
        }
    }
}
function c(&$item, &$key)
{
    global $APPLICATION;
    $item = $APPLICATION->ConvertCharset($item,'windows-1251','UTF-8');
}
require($_SERVER["DOCUMENT_ROOT"]."/bitrix/footer.php");?>
```

В админке битрикса, для сайтов установить кодировку utf-8.
После всех действий выполнить проверку сайта и устранить возможные ошибки.