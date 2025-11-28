### Добавить тенанта 
Шаг 1. 
##### Вводная
В контексте проекта тенант (tenant,арендатор) — это логически изолированное пространство внутри приложения. Представьте это как отдельную "виртуальную" копию программы для конкретного клиента или организации. Для хранения списка этих изолированных пространств (арендаторов) будет использоваться таблица acc_tenants.
##### Описание шага:
Мы начинаем работу с создания тенанта под  названием "VSK" в таблице acc_tenants. Тенант "VSK" -  наш первый и главный арендатор в системе.
Он обеспечивает логическую изоляцию данных для всех учетных записей (аккаунтов), которые будут созданы на начальном этапе работы приложения.
тенант  создается один раз при инициализации таблицы. Для того, чтобы создать тенант а выполнить след. действия:

Выполнить прямой SQL-запрос INSERT в таблицу acc_tenants
```
INSERT INTO acc_tenants (id, code, name, is_deleted, created_at)
VALUES ('1', 'VSK', 'Вск. Продажа договоров страхования' 'false', '2025-11-20T15:30:00Z');
```
ИЛИ 
Использовать метод POST /tnts, где на вход передать значение code = VSK И name = ВСК (см. описание метода <a href="https://github.com/AsyaBarinova/PoliTechDoc/blob/patch-1/auth/acc_tenants.md">https://github.com/AsyaBarinova/PoliTechDoc/blob/patch-1/auth/acc_tenants.md</a>&nbsp;)

<p>Результат шага: создан тенант </p>
<p>Пример записи в таблице:</p> 
<table border="1" style="border-collapse: collapse; width: 100%; height: 72px;">
<tbody>
<tr style="height: 18px;">
<td style="width: 25%; height: 18px;">id</td>
<td style="width: 25%; height: 18px;">code</td>
<td style="width: 25%; height: 18px;">name</td> 
<td style="width: 25%; height: 18px;">is_deleted</td>
<td style="width: 12.5%; height: 18px;">created_at</td>
<td style="width: 12.5%;">updated_at</td>
</tr>
<tr style="height: 18px;">
<td style="width: 25%; height: 18px;">1</td>
<td style="width: 25%; height: 18px;">VSK</td>
<td style="width: 25%; height: 18px;">Вск. Продажа договоров страхования</td> 
<td style="width: 25%; height: 18px;">false</td>
<td style="width: 12.5%; height: 18px;">2025-11-20T15:30:00Z</td>
<td style="width: 12.5%;">NULL</td>
</tr>
</tbody>
</table>
<p></p>
<p></p>

##### Комментарий
<p>На данный момент архитектура приложения предполагает, что абсолютно все будущие аккаунты пользователей будут привязаны к одному-единственному тенанту "VSK".
В будущем, если потребуется поддерживать другю модель, то мы сможем добавлять новых тенантов в эту таблицу. Пока же все работают в рамках одного пространства "VSK".</p>

### Добавить клиента(партнера)
Шаг 2.
##### Вводная
Клиент (партнер) — это внешний контрагент (организация или сервис), который использует функционал нашего приложения. Для хранения информации о партнерских подключениях к тенантам будем использовать таблицу acc_clients. 

##### Описание шага:
Для создания нового Партнера и привязке его к конкретному Тенанту VSK, необходимо добавить соответствующую запись в таблицу acc_clients.
В качестве примера мы создадим партнера под названием "СРАВНИ". Добавление записи можно осуществить следующими способами:

Выполнить прямой SQL-запрос INSERT в таблицу acc_clients
```
INSERT INTO acc_clients (id, tid, client_id, name, is_deleted, created_at )
VALUES ('2', '1', 'СРАВНИ', 'SRAVNI', 'false', '2025-11-20T15:30:00Z');
```
ИЛИ 

Использовать метод POST /tnts/{tenantId}/clients (см. описание метода https://github.com/AsyaBarinova/PoliTechDoc/blob/patch-1/auth/acc_clients.md)
<p>Результат шага: создан партнер, привязанный к определенному тенант у VSK</p>
<p>Пример записи в таблице:</p> 
<table border="1" style="border-collapse: collapse; width: 100%; height: 72px;">
<tbody>
<tr style="height: 18px;">
<td style="width: 25%; height: 18px;">id</td>
  <td style="width: 25%; height: 18px;">tid</td>
<td style="width: 25%; height: 18px;">client_id</td>  
<td style="width: 25%; height: 18px;">default_account_id</td>
  <td style="width: 12.5%; height: 18px;">name</td>
<td style="width: 12.5%;">is_deleted</td>
<td style="width: 12.5%;">created_at</td>  
<td style="width: 12.5%;">updated_at</td>    
</tr>
<tr style="height: 18px;">
<td style="width: 25%; height: 18px;">2</td>
<td style="width: 25%; height: 18px;">1</td>
<td style="width: 25%; height: 18px;">SRAVNI</td>
<td style="width: 12.5%; height: 18px;">NULL</td>
<td style="width: 12.5%;">СРАВНИ</td>
<td style="width: 12.5%;">false</td>  
<td style="width: 12.5%;">2025-11-20T15:30:00Z</td>  
<td style="width: 12.5%;">NULL</td>    
</tr>
</tbody>
</table>
<p></p>
<p></p>

### Добавить пользователя (УЗ)
Шаг 3.
##### Вводная
<p>Учетная запись пользователя содержит следующие данные: логин (электронная почта), пароль, ФИО и должность пользователя, имеющего доступ в систему.</p>
<p>УЗ заводятся в рамках тенанта. При этом одна УЗ может быть привязана к нескольким разным портфелям (account), связь обеспечивает таблица acc_accounts_logins. Для хранения информации об учетных записях используется таблица acc_logins.</p>

###### Создание УЗ
##### Описание шага:
Для создания новой УЗ и привязки тенанту, необходимо добавить соответствующую запись в таблицу acc_logins следующими способами:

Выполнить прямой SQL-запрос INSERT в таблицу acc_logins
```
INSERT INTO acc_logins (id, tid, user_login, full_name, position, is_deleted, created_at)
VALUES ('2', '1', 'olga.p1@gmail.com', 'Пивоварова Ольга Васильевна', 'Страховой агент', 'false', '2025-11-20T15:30:00Z');
```
ИЛИ 

Использовать метод POST /tnts/{tenantId}/logins (см. описание метода https://github.com/ratner28/PoliTechDoc/blob/patch-2/auth/acc_logins.md)

<p>Пример записи в таблице:</p> 
<table border="1" style="border-collapse: collapse; width: 100%; height: 72px;">
<tbody>
<tr style="height: 18px;">
<td style="width: 25%; height: 18px;">id</td>
<td style="width: 25%; height: 18px;">tid</td>
<td style="width: 25%; height: 18px;">user_login</td>
<td style="width: 25%; height: 18px;">password</td>
<td style="width: 25%; height: 18px;">full_name</td>
<td style="width: 12.5%; height: 18px;">position</td>
<td style="width: 12.5%;">is_deleted</td>
<td style="width: 12.5%;">created_at</td>  
<td style="width: 12.5%;">updated_at</td>    
</tr>
<tr style="height: 18px;">
<td style="width: 25%; height: 18px;">2</td>
<td style="width: 25%; height: 18px;">1</td>
<td style="width: 25%; height: 18px;">olga.p1@gmail.com</td>
<td style="width: 12.5%; height: 18px;">null</td>
<td style="width: 12.5%; height: 18px;">Пивоварова Ольга Васильевна</td>
<td style="width: 12.5%;">Страховой агент</td>
<td style="width: 12.5%;">false</td>  
<td style="width: 12.5%;">2025-11-20T15:30:00Z</td>  
<td style="width: 12.5%;">NULL</td>    
</tr>
</tbody>
</table>
<p></p>
<p></p>

###### Добавление (изменение) пароля для УЗ
Для добавления пароля для УЗ необходимо использовать метод POST /api/auth/set-password (см. описание метода https://github.com/ratner28/PoliTechDoc/blob/patch-2/auth/acc_logins.md). Чтобы создать/поменять пароль у пользователя должна быть назначена роль SYS_ADMIN.
После вызова метода пароль хешируется, и его хеш-сумма сохраняется в таблице.

<p>Пример записи в таблице:</p> 
<table border="1" style="border-collapse: collapse; width: 100%; height: 72px;">
<tbody>
<tr style="height: 18px;">
<td style="width: 25%; height: 18px;">id</td>
<td style="width: 25%; height: 18px;">tid</td>
<td style="width: 25%; height: 18px;">user_login</td>
<td style="width: 25%; height: 18px;">password</td>
<td style="width: 25%; height: 18px;">full_name</td>
<td style="width: 12.5%; height: 18px;">position</td>
<td style="width: 12.5%;">is_deleted</td>
<td style="width: 12.5%;">created_at</td>  
<td style="width: 12.5%;">updated_at</td>    
</tr>
<tr style="height: 18px;">
<td style="width: 25%; height: 18px;">2</td>
<td style="width: 25%; height: 18px;">1</td>
<td style="width: 25%; height: 18px;">olga.p1@gmail.com</td>
<td style="width: 12.5%; height: 18px;">3fd90706aa13b38f74ba8e0ca16cd598</td>
<td style="width: 12.5%; height: 18px;">Пивоварова Ольга Васильевна</td>
<td style="width: 12.5%;">Страховой агент</td>
<td style="width: 12.5%;">false</td>  
<td style="width: 12.5%;">2025-11-20T15:30:00Z</td>  
<td style="width: 12.5%;">2025-11-21T15:30:00Z</td>    
</tr>
</tbody>
</table>
<p></p>
<p></p>


### Добавить аккаунт, роли для уз, токен 
Шаг 4.
##### Вводная
<p>Аккаунт (аccount) &mdash; это "виртуальный субъект", от имени которого работают пользователи. К одному account может быть привязано несколько логинов (учётных записей людей).</p>
<p>Это даёт следующие преимущества:</p>
<ul>
<li>несколько сотрудников могут работать с одним и тем же портфелем (например, отдел корпоративных продаж);</li>
<li>один пользователь может иметь доступ к нескольким account (например, агент ведёт несколько страховых портфелей);</li>
<li>при смене сотрудников нет необходимости изменять сам account &mdash; достаточно переназначить привязку логинов.</li>
</ul>
<p dir="auto">У каждого account определяется набор ролей и прав, который определяет:</p>
<ul>
<li dir="auto">какие действия доступны (создание договора, чтение договора и др.);</li>
<li dir="auto">какие продукты можно продовать</li>
</ul>
<p dir="auto">Таким образом, права назначаются на account, а пользователи наследуют эти права через привязку к нему.</p>
<p dir="auto">Account встроен в иерархию компании:</p>
<ul>
<li dir="auto">на верхнем уровне могут быть глобальные account (например, маркетплейс в целом);</li>
<li dir="auto">ниже &mdash; account брокеров или филиалов;</li>
<li dir="auto">ещё ниже &mdash; account отдельных портфелей или команд.</li>
</ul>
<div class="markdown-heading" dir="auto"></div>
<p dir="auto">В страховании account также можно рассматривать как портфель:</p>
<ul>
<li dir="auto">в него входят договоры, клиенты, продукты;</li>
<li dir="auto">к нему привязаны пользователи (агенты, менеджеры);</li>
<li dir="auto">на уровне account настраиваются комиссии, лимиты и права</li>
</ul>
Для хранения списка аакунтов будет использоваться таблица acc_accounts

Токен — это дополнительный идентификатор, доступа к портфелю. Иногда продажи проводятся на публичных сайтах, но есть потребность привязать продажи к разным портфелям. Тогда можно сгенерить разные токены, раздать их продавцам, чтобы они вводили их при продаже и настроить этот код на определенный портфель. Например, при создании акции можно записать токен тем самым и разделить портфель продаж. 
Для хранения списка аакунтов будет использоваться таблица acc_account_tokens

##### Описание шага:

После создания пользователей можем им назначать права. Важно - работа пользователя ведется от определнного аккаунта, поэтому когда наделяются УЗ правами , то создается аккаунт с опред. привелегиями и к созданным уз назначаются роли в разрезе продукта.
В качестве примера мы создадим аккаунт, логин, уз, токен для клиента "СРАВНИ". Добавление записи можно осуществить следующими способами:

1. Выполнить прямой SQL-запрос INSERT в таблицу acc_accounts, acc_products_roles, acc_account_logins (если при заведении известен/требуется токен, то acc_account_tokens)

acc_accounts аккаунт 
<table style="height: 313px; width: 351px;">
<thead>
<tr style="height: 36px;">
<th style="text-align: center; height: 36px; width: 101.727px;">Название поля</th>
<th style="text-align: center; width: 102.136px; height: 36px;">Пример</th>
<th style="text-align: center; height: 36px; width: 148.42px;">Описание</th>
</tr>
</thead>
<tbody>
<tr style="height: 36px;">
<td style="text-align: center; height: 35px; width: 101.727px;">id</td>
<td style="text-align: center; width: 102.136px; height: 35px;">3</td>
<td style="text-align: center; height: 35px; width: 148.42px;">Идентификатор записи</td>
</tr>
<tr style="height: 36px;">
<td style="text-align: center; height: 36px; width: 101.727px;">tid</td>
<td style="text-align: center; width: 102.136px; height: 36px;">1</td>
<td style="text-align: center; height: 36px; width: 148.42px;">Внешний ключ для связи с таблицей acc_tenants.id</td>
</tr>
<tr style="height: 18px;">
<td style="text-align: center; width: 101.727px; height: 18px;">client_id</td>
<td style="text-align: center; width: 102.136px; height: 18px;">SRAVNI</td>
<td style="text-align: center; width: 148.42px; height: 18px;">Внешний ключ для связи с таблицей acc_clients.clients_id</td>
</tr>
<tr style="height: 72px;">
<td style="text-align: center; height: 80px; width: 101.727px;">parent_id</td>
<td style="text-align: center; width: 102.136px; height: 80px;"><span>3</span></td>
<td style="text-align: center; height: 80px; width: 148.42px;">Родитель acc_accounts.id</td>
</tr>
<tr style="height: 36px;">
<td style="text-align: center; height: 36px; width: 101.727px;">accounts_type</td>
<td style="text-align: center; width: 102.136px; height: 36px;"><span>ACCOUNT</span></td>
<td style="text-align: center; height: 36px; width: 148.42px;">
<p>Тип узла</p>
<p></p>
</td>
</tr>
<tr style="height: 18px;">
<td style="text-align: center; width: 101.727px; height: 18px;">name</td>
<td style="text-align: center; width: 102.136px; height: 18px;"><span>Аккаунт для продаж СРАВНИ</span></td>
<td style="text-align: center; width: 148.42px; height: 18px;">Наименование</td>
</tr>
<tr style="height: 18px;">
<td style="text-align: center; width: 101.727px; height: 18px;">created_at</td>
<td style="text-align: center; width: 102.136px; height: 18px;">2025-11-20T15:30:00Z</td>
<td style="text-align: center; width: 148.42px; height: 18px;">Дата / время созадния</td>
</tr>
</tbody>
</table>
 
acc_products_roles роли 
<table style="height: 419px; width: 375px;">
<thead>
<tr style="height: 36px;">
<th style="text-align: center; height: 36px; width: 117.909px;">Название поля</th>
<th style="text-align: center; width: 102.136px; height: 36px;">Пример</th>
<th style="text-align: center; height: 36px; width: 156.352px;">Описание</th>
</tr>
</thead>
<tbody>
<tr style="height: 36px;">
<td style="text-align: center; height: 35px; width: 117.909px;">id</td>
<td style="text-align: center; width: 102.136px; height: 35px;">4</td>
<td style="text-align: center; height: 35px; width: 156.352px;">Идентификатор записи</td>
</tr>
<tr style="height: 36px;">
<td style="text-align: center; height: 36px; width: 117.909px;">tid</td>
<td style="text-align: center; width: 102.136px; height: 36px;">1</td>
<td style="text-align: center; height: 36px; width: 156.352px;">Внешний ключ для связи с таблицей acc_tenants.id</td>
</tr>
<tr style="height: 72px;">
<td style="text-align: center; height: 80px; width: 117.909px;">role_products_id</td>
<td style="text-align: center; width: 102.136px; height: 80px;">5</td>
<td style="text-align: center; height: 80px; width: 156.352px;">Внешний ключ для связи с таблицей products.id</td>
</tr>
<tr style="height: 36px;">
<td style="text-align: center; height: 36px; width: 117.909px;">role_account_id</td>
<td style="text-align: center; width: 102.136px; height: 36px;">3</td>
<td style="text-align: center; height: 36px; width: 156.352px;">Внешний ключ для связи с таблицей acc_accounts.id</td>
</tr>
<tr style="height: 18px;">
<td style="text-align: center; width: 117.909px; height: 18px;">is_deleted</td>
<td style="text-align: center; width: 102.136px; height: 18px;">false</td>
<td style="text-align: center; width: 156.352px; height: 18px;">Флаг удаления. True - неактивный(удален), false - активынй</td>
</tr>
<tr style="height: 18px;">
<td style="text-align: center; width: 117.909px; height: 18px;">can_read</td>
<td style="text-align: center; width: 102.136px; height: 18px;">true</td>
<td style="text-align: center; width: 156.352px; height: 18px;">Разрешение на чтение &nbsp;</td>
</tr>
<tr style="height: 18px;">
<td style="text-align: center; width: 117.909px; height: 18px;">can_printform</td>
<td style="text-align: center; width: 102.136px; height: 18px;">true</td>
<td style="text-align: center; width: 156.352px; height: 18px;">Разрешение на получение ПФ &nbsp;</td>
</tr>
<tr style="height: 36px;">
<td style="text-align: center; height: 36px; width: 117.909px;">can_quote</td>
<td style="text-align: center; width: 102.136px; height: 36px;">true</td>
<td style="text-align: center; height: 36px; width: 156.352px;">Разрешение на пред. расчет</td>
</tr>
<tr style="height: 17px;">
<td style="text-align: center; width: 117.909px; height: 17px;">can_policy</td>
<td style="text-align: center; width: 102.136px; height: 17px;">true</td>
<td style="text-align: center; width: 156.352px; height: 17px;">Разрешение на итог. расчет</td>
</tr>
<tr style="height: 18px;">
<td style="text-align: center; width: 117.909px; height: 18px;">can_addendum</td>
<td style="text-align: center; width: 102.136px; height: 18px;">false</td>
<td style="text-align: center; width: 156.352px; height: 18px;">Разрешение на создание&nbsp; доп.соглашен</td>
</tr>
<tr style="height: 17px;">
<td style="text-align: center; width: 117.909px; height: 17px;">can_cancel</td>
<td style="text-align: center; width: 102.136px; height: 17px;">true</td>
<td style="text-align: center; width: 156.352px; height: 17px;">Разрешение на аннулирование договора</td>
</tr>
<tr style="height: 18px;">
<td style="text-align: center; width: 117.909px; height: 18px;">can_prolongate</td>
<td style="text-align: center; width: 102.136px; height: 18px;">false</td>
<td style="text-align: center; width: 156.352px; height: 18px;">Разрешение на пролонгацию договора</td>
</tr>
<tr style="height: 18px;">
<td style="text-align: center; width: 117.909px; height: 18px;">created_at</td>
<td style="text-align: center; width: 102.136px; height: 18px;">2025-11-20T15:30:00Z</td>
<td style="text-align: center; width: 156.352px; height: 18px;">Дата/время создания&nbsp;</td>
</tr>
</tbody>
</table>

pt_products подключаемый продукт 
 <table style="height: 331px; width: 330px;">
<thead>
<tr style="height: 36px;">
<th style="text-align: center; height: 36px; width: 118.045px;">Название поля</th>
<th style="text-align: center; width: 93.5909px; height: 36px;">Пример</th>
<th style="text-align: center; height: 36px; width: 113.977px;">Описание</th>
</tr>
</thead>
<tbody>
<tr style="height: 36px;">
<td style="text-align: center; height: 35px; width: 118.045px;">id</td>
<td style="text-align: center; width: 93.5909px; height: 35px;">5</td>
<td style="text-align: center; height: 35px; width: 113.977px;">Идентификатор продукта</td>
</tr>
<tr style="height: 36px;">
<td style="text-align: center; height: 36px; width: 118.045px;">tid</td>
<td style="text-align: center; width: 93.5909px; height: 36px;">1</td>
<td style="text-align: center; height: 36px; width: 113.977px;">Внешний ключ для связи с таблицей acc_tenants.id</td>
</tr>
<tr style="height: 72px;">
<td style="text-align: center; height: 80px; width: 118.045px;">code</td>
<td style="text-align: center; width: 93.5909px; height: 80px;"><span>Accident </span></td>
<td style="text-align: center; height: 80px; width: 113.977px;">Код продукта</td>
</tr>
<tr style="height: 72px;">
<td style="text-align: center; width: 118.045px; height: 72px;">name</td>
<td style="text-align: center; width: 93.5909px; height: 72px;">Страхование от несчастных случаев (НС)</td>
<td style="text-align: center; width: 113.977px; height: 72px;">Наименование продукта</td>
</tr>
<tr style="height: 18px;">
<td style="text-align: center; width: 118.045px; height: 18px;">lob</td>
<td style="text-align: center; width: 93.5909px; height: 18px;">Страхование жизни</td>
<td style="text-align: center; width: 113.977px; height: 18px;">Линия бизнеса</td>
</tr>
<tr style="height: 18px;">
<td style="text-align: center; width: 118.045px; height: 18px;">prod_version_no</td>
<td style="text-align: center; width: 93.5909px; height: 18px;">1</td>
<td style="text-align: center; width: 113.977px; height: 18px;">Версия продукта прод</td>
</tr>
<tr style="height: 18px;">
<td style="text-align: center; width: 118.045px; height: 18px;">dev_version_no</td>
<td style="text-align: center; width: 93.5909px; height: 18px;">1</td>
<td style="text-align: center; width: 113.977px; height: 18px;">Версия продукта dev</td>
</tr>
</tbody>
</table>

acc_account_tokens токен
<table style="height: 277px; width: 330px;">
<thead>
<tr style="height: 36px;">
<th style="text-align: center; height: 36px; width: 77.6023px;">Название поля</th>
<th style="text-align: center; width: 102.136px; height: 36px;">Пример</th>
<th style="text-align: center; height: 36px; width: 148.42px;">Описание</th>
</tr>
</thead>
<tbody>
<tr style="height: 36px;">
<td style="text-align: center; height: 35px; width: 77.6023px;">id</td>
<td style="text-align: center; width: 102.136px; height: 35px;">4</td>
<td style="text-align: center; height: 35px; width: 148.42px;">Идентификатор записи</td>
</tr>
<tr style="height: 36px;">
<td style="text-align: center; height: 36px; width: 77.6023px;">tid</td>
<td style="text-align: center; width: 102.136px; height: 36px;">1</td>
<td style="text-align: center; height: 36px; width: 148.42px;">Внешний ключ для связи с таблицей acc_tenants.id</td>
</tr>
<tr style="height: 18px;">
<td style="text-align: center; width: 77.6023px; height: 18px;">token</td>
<td style="text-align: center; width: 102.136px; height: 18px;">PROMO</td>
<td style="text-align: center; width: 148.42px; height: 18px;">Название токена</td>
</tr>
<tr style="height: 18px;">
<td style="text-align: center; width: 77.6023px; height: 18px;">client_id</td>
<td style="text-align: center; width: 102.136px; height: 18px;">SRAVNI</td>
<td style="text-align: center; width: 148.42px; height: 18px;">Внешний ключ для связи с таблицей acc_clients.clients_id</td>
</tr>
<tr style="height: 18px;">
<td style="text-align: center; width: 77.6023px; height: 18px;">aid</td>
<td style="text-align: center; width: 102.136px; height: 18px;">3</td>
<td style="text-align: center; width: 148.42px; height: 18px;">Внешний ключ для связи с таблицей acc_accounts.id</td>
</tr>
<tr style="height: 36px;">
<td style="text-align: center; height: 36px; width: 77.6023px;">is_deleted</td>
<td style="text-align: center; width: 102.136px; height: 36px;">false</td>
<td style="text-align: center; height: 36px; width: 148.42px;">Флаг удаления. true - да, false-нет</td>
</tr>
<tr style="height: 72px;">
<td style="text-align: center; height: 80px; width: 77.6023px;">created_at</td>
<td style="text-align: center; width: 102.136px; height: 80px;">2025-11-20T15:30:00Z</td>
<td style="text-align: center; height: 80px; width: 148.42px;">Дата/время создания</td>
</tr>
</tbody>
</table>

acc_account_logins привязка пользователя к аккаунту

<table style="height: 295px; width: 354px;">
<thead>
<tr style="height: 36px;">
<th style="text-align: center; height: 36px; width: 80.1591px;">Название поля</th>
<th style="text-align: center; width: 110.057px; height: 36px;">Пример</th>
<th style="text-align: center; height: 36px; width: 146.148px;">Описание</th>
</tr>
</thead>
<tbody>
<tr style="height: 36px;">
<td style="text-align: center; height: 35px; width: 80.1591px;">id</td>
<td style="text-align: center; width: 110.057px; height: 35px;">5</td>
<td style="text-align: center; height: 35px; width: 146.148px;">Идентификатор записи</td>
</tr>
<tr style="height: 36px;">
<td style="text-align: center; height: 36px; width: 80.1591px;">tid</td>
<td style="text-align: center; width: 110.057px; height: 36px;">1</td>
<td style="text-align: center; height: 36px; width: 146.148px;">Внешний ключ для связи с таблицей acc_tenants.id</td>
</tr>
<tr style="height: 18px;">
<td style="text-align: center; width: 80.1591px; height: 18px;">user_login</td>
<td style="text-align: center; width: 110.057px; height: 18px;"><span>olga.p1@gmail.com</span></td>
<td style="text-align: center; width: 146.148px; height: 18px;">Логин пользователя</td>
</tr>
<tr style="height: 18px;">
<td style="text-align: center; width: 80.1591px; height: 18px;">client_id</td>
<td style="text-align: center; width: 110.057px; height: 18px;">SRAVNI</td>
<td style="text-align: center; width: 146.148px; height: 18px;">Внешний ключ для связи с таблицей acc_clients.client_id&nbsp;</td>
</tr>
<tr style="height: 18px;">
<td style="text-align: center; width: 80.1591px; height: 18px;">is_default</td>
<td style="text-align: center; width: 110.057px; height: 18px;">true</td>
<td style="text-align: center; width: 146.148px; height: 18px;">
<p>Дефолтный портефль&nbsp;</p>
<p>true - да, false - нет</p>
</td>
</tr>
<tr style="height: 18px;">
<td style="text-align: center; width: 80.1591px; height: 18px;">aid</td>
<td style="text-align: center; width: 110.057px; height: 18px;">3</td>
<td style="text-align: center; width: 146.148px; height: 18px;">Внешний ключ для связи с таблицей acc_account.id&nbsp;</td>
</tr>
<tr style="height: 72px;">
<td style="text-align: center; height: 80px; width: 80.1591px;">created_at</td>
<td style="text-align: center; width: 110.057px; height: 80px;">2025-11-20T15:30:00Z</td>
<td style="text-align: center; height: 80px; width: 146.148px;">Дата/время создания</td>
</tr>
<tr style="height: 36px;">
<td style="text-align: center; height: 36px; width: 80.1591px;">user_role</td>
<td style="text-align: center; width: 110.057px; height: 36px;">SALES</td>
<td style="text-align: center; height: 36px; width: 146.148px;">Роль пользователя</td>
</tr>
</tbody>
</table>

<td> ИЛИ </td>

Использовать метод REST API: POST /tnts/{tenantCode}/clients/{clientId}/accounts
<td>Важно: ДО выполнения запроса должен быть создан подключаемый продукт в таблице pt_products И логин(ы) пользователя в таблице account_loginsс.</td> 

##### Дополнительный УЗ к созданному аккаунту 
<td>Если требуется создать новые УЗ к аккаунту, то также ипользуем метод POST /tnts/{tenantCode}/clients/{clientId}/accounts</td> 

##### Дополнительный токен к созданному аккаунту 



