# Дроздов Юрий.
# Домашнее задание к занятию 6.

создайте виртуальную машину c Ubuntu 20.04 LTS (bionic) в GCE типа e2-medium в default VPC в любом регионе и зоне, например us-central1-a

    Виртуальная машина postgres2022 создана в Яндекс.Облако


поставьте на нее PostgreSQL 14 через sudo apt

    sudo apt update && sudo apt upgrade -y && sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list' && wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add - && sudo apt-get update && sudo apt-get -y install postgresql


проверьте что кластер запущен через sudo -u postgres pg_lsclusters
```
Ver Cluster Port Status Owner    Data directory              Log file
14  main    5432 online postgres /var/lib/postgresql/14/main /var/log/postgresql/postgresql-14-main.log
```


зайдите из под пользователя postgres в psql и сделайте произвольную таблицу с произвольным содержимым postgres=# create table test(c1 text); postgres=# insert into test values('1'); \q
```
postgres=# create table test(c1 text);
CREATE TABLE
postgres=# insert into test values('1'); \q
INSERT 0 1
```



остановите postgres например через sudo -u postgres pg_ctlcluster 14 main stop
```
Ver Cluster Port Status Owner    Data directory              Log file
14  main    5432 down   postgres /var/lib/postgresql/14/main /var/log/postgresql/postgresql-14-main.log
```


создайте новый standard persistent диск GKE через Compute Engine -> Disks в том же регионе и зоне что GCE инстанс размером например 10GB

    Создан диск размером 5 Гбс названием postgres-disk2


добавьте свеже-созданный диск к виртуальной машине - надо зайти в режим ее редактирования и дальше выбрать пункт attach existing disk

    Выполнено


проинициализируйте диск согласно инструкции и подмонтировать файловую систему, только не забывайте менять имя диска на актуальное, в вашем случае это скорее всего будет /dev/sdb - https://www.digitalocean.com/community/tutorials/how-to-partition-and-format-storage-devices-in-linux

    Выполнено. Диск был /dev/vdb


перезагрузите инстанс и убедитесь, что диск остается примонтированным (если не так смотрим в сторону fstab)

    Диск на месте


сделайте пользователя postgres владельцем /mnt/data - chown -R postgres:postgres /mnt/data/

    Выполнено


перенесите содержимое /var/lib/postgres/14 в /mnt/data - mv /var/lib/postgresql/14 /mnt/data

    Выполнено


попытайтесь запустить кластер - sudo -u postgres pg_ctlcluster 14 main start
напишите получилось или нет и почему

    Кластер не запустился, так как отсутствует директория с данными


задание: найти конфигурационный параметр в файлах раположенных в /etc/postgresql/14/main который надо поменять и поменяйте его
напишите что и почему поменяли
```
Необходимо указать новый путь к директории с данными.
Была изменена строка:
data_directory = '/var/lib/postgresql/14/main'
на
data_directory = '/mnt/data/14/main'
```


попытайтесь запустить кластер - sudo -u postgres pg_ctlcluster 14 main start
напишите получилось или нет и почему

    Кластер запустился:
    Ver Cluster Port Status Owner    Data directory    Log file
    14  main    5432 online postgres /mnt/data/14/main /var/log/postgresql/postgresql-14-main.log


зайдите через через psql и проверьте содержимое ранее созданной таблицы
```
Запись на месте.
postgres=# select * from test;
 c1 
----
 1
(1 row)
```



задание со звездочкой *: не удаляя существующий GCE инстанс сделайте новый, поставьте на его PostgreSQL, удалите файлы с данными из /var/lib/postgres, перемонтируйте внешний диск который сделали ранее от первой виртуальной машины ко второй и запустите PostgreSQL на второй машине так чтобы он работал с данными на внешнем диске, расскажите как вы это сделали и что в итоге получилось.
```
1. Отключение от существующего инстанса.
    1.1 Остановил кластер postgres - sudo -u postgres pg_ctlcluster 14 main stop
    1.2 Размонтировал раздел с базой данных - umount /mnt/data
    1.3 Выключил ВМ
    1.4 В консоли Яндекс.Облако отсоединил диск postgres-disk2
2. Создание новго инстанса
    2.1 Создал ВМ postgres2022-2 в Яндекс.Облако
    2.2 Установил Postgres
    2.3 Остановил кластер Postgres
    2.4 Удалил файлы с данными из /var/lib/postresql - rm -R /var/lib/postgresql/*
3. Подключение внешнего диска и настройка работы Postgres
    3.1 В консоли Яндекс.Облако присоединил диск postgres-disk2 к ВМ postgres2022-2
    3.2 Примонтировал /dev/vdb1 в /var/lib/postgresql (в fstab, потом 'mount -a')
    3.3 Сменил владельца каталога /var/lib/postgresql на пользователя postgres - chown -R postgres:postgres /var/lib/postgresql
    3.4 Запустил кластер Postgres:
            Ver Cluster Port Status Owner    Data directory              Log file
            14  main    5432 online postgres /var/lib/postgresql/14/main /var/log/postgresql/postgresql-14-main.log
    3.5 Проверил наличие записи в таблице. Запись на месте
            root@postgres2022-2:~# su - postgres
            postgres@postgres2022-2:~$ psql
            psql (14.2 (Ubuntu 14.2-1.pgdg20.04+1+b1))
            Type "help" for help.
            
            postgres=# select * from test;
             c1 
            ----
             1
            (1 row)
