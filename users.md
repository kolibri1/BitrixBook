Пользователи
============

Получить пользователя по id
---------------------------
```php
use Bitrix\Main\UserTable;
$user = UserTable::getById(1)->fetch();
print $user["EMAIL"];
```

Получить всех зарегистрированных пользователей
---------------------------
```php
use Bitrix\Main\UserTable;
$users = UserTable::getList();
while ($user = $users->fetch()) {
	print $user["EMAIL"]."<br>";
}
```
