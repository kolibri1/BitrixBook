Копирование элемента инфоблока
==============================

```php
function copyElement($id,$name = "") {
	$resource = CIBlockElement::GetByID($id);
	if ($ob = $resource->GetNextElement())
	{
	   $arFields = $ob->GetFields();
	   $arFields['PROPERTIES'] = $ob->GetProperties();
	   
		$arFieldsCopy["IBLOCK_ID"] = $arFields["IBLOCK_ID"];
		$arFieldsCopy["IBLOCK_SECTION_ID"] = $arFields["IBLOCK_SECTION_ID"];
		$arFieldsCopy["PREVIEW_TEXT_TYPE"] = "html";
		$arFieldsCopy["PREVIEW_TEXT"] = $arFields["PREVIEW_TEXT"];
		$arFieldsCopy["DETAIL_TEXT_TYPE"] = "html";
		$arFieldsCopy["DETAIL_TEXT"] = $arFields["DETAIL_TEXT"];

		unset($arFieldsCopy["MIN_PRICE"]);

		$arFieldsCopy["NAME"] = ($name)?$name:$arFields["NAME"];

		$transParams = array("replace_space"=>"-","replace_other"=>"-");
		$trans = Cutil::translit($arFieldsCopy["NAME"],"ru",$transParams);
		$arFieldsCopy["CODE"] = $trans;

		$arFieldsCopy['PREVIEW_PICTURE'] = CFile::MakeFileArray(CFile::CopyFile($arFields['PREVIEW_PICTURE']));
		$arFieldsCopy['DETAIL_PICTURE'] = CFile::MakeFileArray(CFile::CopyFile($arFields['DETAIL_PICTURE']));

		//привязка к разделам
		$db_old_groups = CIBlockElement::GetElementGroups($arFields['ID'], true);
	        while($ar_group = $db_old_groups->Fetch()) {
				if(!in_array($ar_group['ID'],$arFields['IBLOCK_SECTION'])) {
	                $arFieldsCopy['IBLOCK_SECTION'][]=$ar_group['ID'];
				}
	        }

	   //перенос свойств
	   $arFieldsCopy['PROPERTY_VALUES'] = array();
	   foreach ($arFields['PROPERTIES'] as $property)
	   {
	        $arFieldsCopy['PROPERTY_VALUES'][$property['CODE']] = $property['VALUE'];

	        if ($property['PROPERTY_TYPE']=='L')
				$arFieldsCopy['PROPERTY_VALUES'][$property['CODE']]['VALUE_ENUM_ID'] = $property['VALUE_ENUM_ID'];

			if ($property['PROPERTY_TYPE']=='F')
			{
			   if ($property['MULTIPLE']=='Y') {
			     if (is_array($property['VALUE']))
			     {
			        foreach ($property['VALUE'] as $key => $arElEnum) 
			         $arFieldsCopy['PROPERTY_VALUES'][$property['CODE']][$key]=CFile::CopyFile($arElEnum);                             
			     }                 
			   }else $arFieldsCopy['PROPERTY_VALUES'][$property['CODE']] = CFile::CopyFile($property['VALUE']);
			}
	   }
	   
	   $ele = new CIBlockElement();
	   if($NEW_ID = $ele->Add($arFieldsCopy)){
	   		echo 'Элемент скопирован. ID нового элемента: '.$NEW_ID;
	   		return $NEW_ID;
	   } else {
			echo 'Ошибка копирования элемента с ID: '.$arFields["ID"];
			print "<pre>";
			print_r($ele->LAST_ERROR);
			print "</pre>";
			return false;
	   }
	   unset($arFields);
	}
	return false;
}
```
