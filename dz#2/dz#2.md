# Дроздов Юрий.
# Домашнее задание к занятию 2.
# Работа с уровнями изоляции транзакции в PostgreSQL

создать новый проект в Google Cloud Platform, например postgres2022-, где yyyymmdd год, месяц и день вашего рождения (имя проекта должно быть уникально на уровне GCP)
дать возможность доступа к этому проекту пользователю ifti@yandex.ru с ролью Project Editor
далее создать инстанс виртуальной машины Compute Engine с дефолтными параметрами
добавить свой ssh ключ в GCE metadata

	Проект с названием postgres2022 был создан в Яндекс.Облако. Создана виртуальная машина с 2 vCPU, 2 Гб ОЗУ, 10 Гб HDD

зайти удаленным ssh (первая сессия), не забывайте про ssh-add

    При создании ВМ был импортирован публичный ssh-ключ c рабочей станции

поставить PostgreSQL

    sudo apt update && sudo apt upgrade -y && sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list' && wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add - && sudo apt-get update && sudo apt-get -y install postgresql


зайти вторым ssh (вторая сессия)
запустить везде psql из под пользователя postgres
выключить auto commit

    \set AUTOCOMMIT OFF

сделать в первой сессии новую таблицу и наполнить ее данными create table persons(id serial, first_name text, second_name text); insert into persons(first_name, second_name) values('ivan', 'ivanov'); insert into persons(first_name, second_name) values('petr', 'petrov'); commit;

    CREATE TABLE
    INSERT 0 1
    INSERT 0 1
    COMMIT

посмотреть текущий уровень изоляции: show transaction isolation level

    read committed

начать новую транзакцию в обоих сессиях с дефолтным (не меняя) уровнем изоляции
в первой сессии добавить новую запись insert into persons(first_name, second_name) values('sergey', 'sergeev');
сделать select * from persons во второй сессии
видите ли вы новую запись и если да то почему?

    Новая запись не видна, так как на уровне изоляции Read Committed не видны незафиксированные параллельной транзакцией данные. 

завершить первую транзакцию - commit;
сделать select * from persons во второй сессии
видите ли вы новую запись и если да то почему?

    Новая запись видна, так как первая транзакция была завершена, данные были зафиксированы.

завершите транзакцию во второй сессии
начать новые но уже repeatable read транзации - set transaction isolation level repeatable read;
в первой сессии добавить новую запись insert into persons(first_name, second_name) values('sveta', 'svetova');
сделать select * from persons во второй сессии
видите ли вы новую запись и если да то почему?

    Запись не видна, так как транзакция из первой сессии не была закончена на момент начала второй.

завершить первую транзакцию - commit;
сделать select * from persons во второй сессии
видите ли вы новую запись и если да то почему?

    Запись по-прежнему не видна, так как на уровне изоляции Repeatable Read вторая транзакция работает со снимком данных на момент старта первого оператора.

завершить вторую транзакцию
сделать select * from persons во второй сессии
видите ли вы новую запись и если да то почему?

    Запись видна, так как изменения первой транзакции уже зафиксированы на момент старта данной транзакции.
