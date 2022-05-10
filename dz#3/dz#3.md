# Дроздов Юрий.
# Домашнее задание к занятию 3.
# Установка и настройка PostgteSQL в контейнере Docker


• сделать в GCE инстанс с Ubuntu 20.04

    Создана виртуальная машина в Яндекс.Облаке с Ubuntu 20.04


• поставить на нем Docker Engine

    Ставим docker скриптом и даем текущему пользователю права на управление, добавив в группу docker:
    
    curl -fsSL https://get.docker.com -o get-docker.sh
    sudo sh get-docker.sh
    sudo usermod -aG docker $USER


• сделать каталог /var/lib/postgres

    sudo mkdir /var/lib/postgres


• развернуть контейнер с PostgreSQL 14 смонтировав в него /var/lib/postgres

    Создаем отдельную сеть внутри docker:
    sudo docker network create pg-net

    Запускаем docker-контейнер pg-docker c Postgres 14, указываем сеть для контейнера, пароль пользователя postgres, проброс порта из контейнера в сеть pg-net, а также монтируем каталог /var/lib/postgres внутрь контейнера:
    sudo docker run --name pg-docker --network pg-net -e POSTGRES_PASSWORD=postgres123 -d -p 5432:5432 -v /var/lib/postgres:/var/lib/postgresql/data postgres:14


• развернуть контейнер с клиентом postgres.

    Запуск еще одного контейнера с Postgres 14, подключение в интерактивном режиме к его терминалу, запуск psql с параметрами подключения к первому контейнеру с postgres:
    sudo docker run -it --rm --network pg-net --name pg-client postgres:14 psql -h pg-docker -U postgres
    

• подключится из контейнера с клиентом к контейнеру с сервером и сделать
таблицу с парой строк

    postgres=# create table persons(id serial, first_name text, second_name text); insert into persons(first_name, second_name) values('ivan', 'ivanov'); insert into persons(first_name, second_name) values('petr', 'petrov');
    CREATE TABLE
    INSERT 0 1
    INSERT 0 1


• подключится к контейнеру с сервером с ноутбука/компьютера извне инстансов GCP

    Подключился к postgres с домашнего компьютера из dbeaver без дополнительных настроек инстанса. Настройки по-умолчанию docker-образа postgres разрешают подключение отовсюду с паролем, сервис Postgres слушает все интерфейсы.


• удалить контейнер с сервером

    sudo docker stop pg-docker
    sudo docker rm pg-docker


• создать его заново

    sudo docker run --name pg-docker --network pg-net -e POSTGRES_PASSWORD=postgres123 -d -p 5432:5432 -v /var/lib/postgres:/var/lib/postgresql/data postgres:14
    

• подключится снова из контейнера с клиентом к контейнеру с сервером

    sudo docker run -it --rm --network pg-net --name pg-client postgres:14 psql -h pg-docker -U postgres


• проверить, что данные остались на месте

    select * from persons;
      1 | ivan       | ivanov
      2 | petr       | petrov
