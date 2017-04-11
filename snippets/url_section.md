Код раздела из URL
==============================

```php
$smartBase = "#SECTION_CODE_PATH#/";
$arDefaultUrlTemplates404 = array(
        "sections" => "",
        "section" => "#SECTION_CODE_PATH#/",
        "element" => "#SECTION_CODE_PATH#/#ELEMENT_CODE#/",
);


$arDefaultVariableAliases404 = array();

$arDefaultVariableAliases = array();

$arComponentVariables = array(
    "SECTION_ID",
    "SECTION_CODE",
    "ELEMENT_ID",
    "ELEMENT_CODE",
    "action",
);
$arVariables = array();

$engine = new CComponentEngine($this);
if (\Bitrix\Main\Loader::includeModule('iblock'))
{
    $engine->addGreedyPart("#SECTION_CODE_PATH#");
    $engine->addGreedyPart("#SMART_FILTER_PATH#");
    $engine->setResolveCallback(array("CIBlockFindTools", "resolveComponentEngine"));
}
$arUrlTemplates = CComponentEngine::MakeComponentUrlTemplates($arDefaultUrlTemplates404, $smartBase);
$arVariableAliases = CComponentEngine::MakeComponentVariableAliases($arDefaultVariableAliases404, array());

$componentPage = $engine->guessComponentPath(
    "/shop_new/",
    $arUrlTemplates,
    $arVariables
);
```