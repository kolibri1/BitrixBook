Пользователи
============

Получить пользователя по id

```php
use Bitrix\Main\UserTable;
$user = UserTable::getById(1)->fetch();
print $user["EMAIL"];
```

Получить всех зарегистрированных пользователей

```php
use Bitrix\Main\UserTable;
$users = UserTable::getList();
while ($user = $users->fetch()) {
	print $user["EMAIL"]."<br>";
}
```
К сожалению, пока это все. Если нужны методы add, update и delete пользуйтесь методами старого класса CUser.  
