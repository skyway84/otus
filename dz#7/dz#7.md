# Дроздов Юрий.
# Домашнее задание к занятию 7.
# Работа с базами данных, пользователями и правами.

1 создайте новый кластер PostgresSQL 13 (на выбор - GCE, CloudSQL)
```
Создана ВМ postgres2022 в Яндекс.Облако
```


2 зайдите в созданный кластер под пользователем postgres

    Выполнено


3 создайте новую базу данных testdb
```
postgres=# create database testdb;
CREATE DATABASE
```



4 зайдите в созданную базу данных под пользователем postgres
```
postgres=# \c testdb 
You are now connected to database "testdb" as user "postgres".
```



5 создайте новую схему testnm
```
testdb=# create schema testnm;
CREATE SCHEMA
```



6 создайте новую таблицу t1 с одной колонкой c1 типа integer
```
testdb=# create table testnm.t1(c1 integer);
CREATE TABLE
```



7 вставьте строку со значением c1=1
```
testdb=# insert into testnm.t1 values('1');
INSERT 0 1
```


8 создайте новую роль readonly
```
testdb=# create role readonly;
CREATE ROLE
```


9 дайте новой роли право на подключение к базе данных testdb
```
testdb=# GRANT CONNECT ON DATABASE testdb to readonly;
GRANT
```


10 дайте новой роли право на использование схемы testnm
```
testdb=# GRANT USAGE ON SCHEMA testnm TO readonly;
GRANT
```


11 дайте новой роли право на select для всех таблиц схемы testnm
```
testdb=# GRANT SELECT ON ALL TABLES IN SCHEMA testnm TO readonly;
GRANT
```


12 создайте пользователя testread с паролем test123
```
testdb=# create user testread with password 'test123';
CREATE ROLE
```


13 дайте роль readonly пользователю testread
```
testdb=# grant readonly TO testread;
GRANT ROLE
```


14 зайдите под пользователем testread в базу данных testdb
```
Получил:
testdb=# \c testdb testread
connection to server on socket "/var/run/postgresql/.s.PGSQL.5432" failed: FATAL:  Peer authentication failed for user "testread"
Previous connection kept

Подключился по сети:
postgres@postgres2022:~$ psql -h 127.0.0.1 -U testread -d testdb -W
```

15 сделайте select * from t1;
```
select * from t1;
ERROR:  relation "t1" does not exist
LINE 1: select * from t1;
```


16 получилось? (могло если вы делали сами не по шпаргалке и не упустили один существенный момент про который позже)
```
Не получилось, потому, что для пользователя testread нет testnm схемы в search_path, поэтому необходимо либо явно указывать схему, в которой находится таблица t1, либо указать схему testnm в search_path пользователя.

Я выбираю второй вариант:
SET search_path TO testnm,public;
```

17 напишите что именно произошло в тексте домашнего задания
```
Если строго следовать этапам домашнего задания, то в пункте 6 таблица t1 будет создана в схеме public. В тот момент я подумал, что логичнее ее создать во вновь созданной схеме testnm.
```


18 у вас есть идеи почему? ведь права то дали?
19 посмотрите на список таблиц
```
testdb=> \dt
        List of relations
 Schema | Name | Type  |  Owner   
--------+------+-------+----------
 testnm | t1   | table | postgres
(1 row)
```


20 подсказка в шпаргалке под пунктом 20
21 а почему так получилось с таблицей (если делали сами и без шпаргалки то может у вас все нормально)
22 вернитесь в базу данных testdb под пользователем postgres
23 удалите таблицу t1
```
testdb=# drop table testnm.t1;
DROP TABLE
```


24 создайте ее заново но уже с явным указанием имени схемы testnm
```
testdb=# create table testnm.t1(c1 integer);
CREATE TABLE
```


25 вставьте строку со значением c1=1
```
testdb=# insert into testnm.t1 values('1');
INSERT 0 1
```


26 зайдите под пользователем testread в базу данных testdb
27 сделайте select * from testnm.t1;
28 получилось?
```
Нет
```


29 есть идеи почему? если нет - смотрите шпаргалку
```
Нет прав у пользователя testread на таблицу t1
```


30 как сделать так чтобы такое больше не повторялось? если нет идей - смотрите шпаргалку
```
1. Дать права на чтение существующих таблиц в схеме testnm
GRANT SELECT ON ALL TABLES IN SCHEMA testnm TO readonly;

2. Изменить права по-умолчанию для схемы testnm.
ALTER DEFAULT PRIVILEGES IN SCHEMA testnm GRANT SELECT ON TABLES TO readonly;
```

31 сделайте select * from testnm.t1;
32 получилось?

    Да


33 есть идеи почему? если нет - смотрите шпаргалку
31 сделайте select * from testnm.t1;
32 получилось?

    Да


33 ура!
34 теперь попробуйте выполнить команду create table t2(c1 integer); insert into t2 values (2);
```
testdb=> create table t2(c1 integer); insert into t2 values (2);
ERROR:  permission denied for schema testnm
LINE 1: create table t2(c1 integer);
                     ^
ERROR:  relation "t2" does not exist
LINE 1: insert into t2 values (2);
```


35 а как так? нам же никто прав на создание таблиц и insert в них под ролью readonly?
36 есть идеи как убрать эти права? если нет - смотрите шпаргалку
37 если вы справились сами то расскажите что сделали и почему, если смотрели шпаргалку - объясните что сделали и почему выполнив указанные в ней команды
```
Читал шпаргалку, что произошло.
Из-за того, что в пункте 16 я добавил новую схему в search_path, то попытка создать таблицу из пункта 34 не была успешной из-за отсутствия прав у роли testread на создание объектов в схеме testnm.
Понимаю, что должен был успешно создать таблицу в схеме public, потому что все новые роли имеют роль public и права на все действия схему в схеме public.
Забираю права у роли public 
revoke CREATE on SCHEMA public FROM public; 
revoke all on DATABASE testdb FROM public;

Запоминаю про ограничение роли public в проде.
```


38 теперь попробуйте выполнить команду create table t3(c1 integer); insert into t2 values (2);
```
Для проверки отсутствия прав у роли testread на создание таблиц в схеме public явно задаю схему public при создании таблицы
create table public.t3(c1 integer); insert into public.t2 values (2);

ERROR:  permission denied for schema public
LINE 1: create table public.t3(c1 integer);
                     ^
ERROR:  relation "public.t2" does not exist
LINE 1: insert into public.t2 values (2);
```

39 расскажите что получилось и почему 
