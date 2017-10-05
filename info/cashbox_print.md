В какой момент происходит саздание и печать чека?
==============================

В классе платежной системы Payment (\bitrix\modules\sale\lib\payment.php) в методе save() есть проверка на наличие ключа PAID среди измененных атрибутов.
Если PAID присутствует, до этого у платежа не было статуса PAID и в настройках платежной системы выставлен чекбокс "печатать чек":
```php
$changedKeys = $this->fields->getChangedKeys();
if (in_array("PAID", $changedKeys))
{
    $originalValues = $this->fields->getOriginalValues();
    /** @var Sale\PaySystem\Service $ps */
    $ps = $this->getPaySystem();
    if (($originalValues["PAID"] != $this->getField("PAID"))
        && ($ps->getField("CAN_PRINT_CHECK") == "Y"))
    {
        /** @var PaymentCollection $col */
        $col = $this->getCollection();
        Cashbox\Internals\Pool::addDoc($col->getOrder()->getInternalId(), $this);
    }
}
```
номер заказа и данные платежа добавляются в пул кассы.

Далее при сохранении заказа, метод save() класса Order(\bitrix\modules\sale\lib\order.php), создаются чеки добавленные в пул и относящиеся к заказу

```php
$res = Cashbox\Internals\Pool::generateChecks($this->getInternalId());
if (!$res->isSuccess())
{
    $errCollection = $res->getErrorCollection();
    foreach ($errCollection as $error)
        $result->addWarning($error);
}
```