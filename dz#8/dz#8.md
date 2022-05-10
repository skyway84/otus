# Дроздов Юрий.
# Домашнее задание к занятию 7.


создать GCE инстанс типа e2-medium и диском 10GB

    Создана ВМ в Яндекс.Облако


установить на него PostgreSQL 14 с дефолтными настройками

    sudo apt update && sudo apt upgrade -y && sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list' && wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add - && sudo apt-get update && sudo apt-get -y install postgresql


применить параметры настройки PostgreSQL из прикрепленного к материалам занятия файла

    Выполнено


выполнить pgbench -i postgres

    Выполнено


запустить pgbench -c8 -P 60 -T 600 -U postgres postgres
дать отработать до конца
дальше настроить autovacuum максимально эффективно
построить график по получившимся значениям
так чтобы получить максимально ровное значение tps
