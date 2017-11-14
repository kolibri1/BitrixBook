Создание выпуска и автоматическая рассылка
==============================

```php
//$arEmail - массив email на которые рассылаем
if(count($arEmail) && CModule::IncludeModule("subscribe")) {
    $posting = new CPosting;
    $arFields = Array(
        "FROM_FIELD" => "FROM <mailfrom@mail.ru>",
        "TO_FIELD" => "",
        "BCC_FIELD" => implode(',', $arEmail),
        "EMAIL_FILTER" => "",
        "SUBJECT" => "Заголовок письма",
        "BODY_TYPE" => ("html"),
        "BODY" => $mailBody,// текст сообщения
        "DIRECT_SEND" => "Y",
        "STATUS" => "D",
        "CHARSET" => "UTF-8",
        "SUBSCR_FORMAT" => ("html"),
        "AUTO_SEND_TIME" => date('d.m.Y').' 8:00:00', //когда делаем рассылку
    );
    
    $ID = $posting->Add($arFields);

    //создаем агента для автоматической рассылки
    if($ID)
    {
        $posting->ChangeStatus($ID, "P");
        //$posting->AutoSend($ID);
        CAgent::AddAgent("CPosting::AutoSend(".$ID.",true);",
         "subscribe", "N", 0, 
         $arFields["AUTO_SEND_TIME"], "Y", 
         $arFields["AUTO_SEND_TIME"]);

    } else {
        echo $posting->LAST_ERROR;
    }

}
```