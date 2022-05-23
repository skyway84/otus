# Дроздов Юрий.
# Домашнее задание к занятию 9.


1. Настройте выполнение контрольной точки раз в 30 секунд.
```
checkpoint_timeout = 30s
checkpoint_completion_target = 0.5

```
2. 10 минут c помощью утилиты pgbench подавайте нагрузку.
```
Включаю логирование контрольных точек:
    ALTER SYSTEM SET log_checkpoints = on;
    SELECT pg_reload_conf();

Выполняю контрольную точку:
    CHECKPOINT;


Сбрасываю статистику контрольных точек:
    SELECT pg_stat_reset_shared('bgwriter');

Определяю текущую позицию WAL:
    postgres=# SELECT pg_current_wal_insert_lsn();
     pg_current_wal_insert_lsn 
    ---------------------------
     0/16FABB8
    (1 row)

Определяю текущий файл записи WAL:
    postgres=# SELECT file_name, upper(to_hex(file_offset)) file_offset
    postgres-# FROM pg_walfile_name_offset('0/16FABB8');
            file_name         | file_offset 
    --------------------------+-------------
     000000010000000000000001 | 6FABB8
    (1 row)

Запускаю тест (Начало 11:26, завершение 11:36):
    pgbench -c8 -P 60 -T 600 -U postgres postgres
```


3. Измерьте, какой объем журнальных файлов был сгенерирован за это время. Оцените, какой объем приходится в среднем на одну контрольную точку.
```
1. По WAL-файлу
Текущая позиция WAL:
0/1DF10CE0

Текущий WAL-файл:
00000001000000000000001D

Вычисляю количство файлов между запуском и завершением теста:
00000001000000000000001D-000000010000000000000001=1C (HEX) = 28 (DEC) WAl
Размер WAL-файла по-умолчанию 16Мб
Определяю объем WAL-файлов
25*16Мб = 448 Мб

2. По журналу postgresql-14-main.log
За время выполнения теста была выполнена 21 контрольная точка.
Суммарный объем записей: 456 Мб
Средний объем: 21,7 Мб

```
4. Проверьте данные статистики: все ли контрольные точки выполнялись точно по расписанию. Почему так произошло?
```
Смотрю статистику:

postgres=# SELECT * FROM pg_stat_bgwriter \gx
-[ RECORD 1 ]---------+------------------------------
checkpoints_timed     | 24
checkpoints_req       | 0
checkpoint_write_time | 297259
checkpoint_sync_time  | 834
buffers_checkpoint    | 42727
buffers_clean         | 0
maxwritten_clean      | 0
buffers_backend       | 4110
buffers_backend_fsync | 0
buffers_alloc         | 4530
stats_reset           | 2022-05-23 08:25:02.264758+00

Все точки выполнялись по расписанию.
Нулевой количество checkpoints_req говорит в том числе о том, что max_wal_size не был достигнут, 
а следовательно нет необходимости его увеличивать.

```
5. Сравните tps в синхронном/асинхронном режиме утилитой pgbench. Объясните полученный результат.
```
1. Синхронный режим
    synchronous_commit = on
    show commit_delay = 0
    commit_siblings = 5

Среднее значение tps 611,19

2. Асинхронный режим
    synchronous_commit = off
    wal_writer_delay = 200ms

Среднее значение tps 2570,32

В режиме асинхронного подтверждения сервер сообщает об успешном завершении сразу, как только транзакция будет завершена 
логически, прежде чем сгенерированные записи WAL фактически будут записаны на диск.

```
6. Создайте новый кластер с включенной контрольной суммой страниц. Создайте таблицу. Вставьте несколько значений. Выключите кластер. Измените пару байт в таблице. Включите кластер и сделайте выборку из таблицы. Что и почему произошло? как проигнорировать ошибку и продолжить работу?
```
6.1. Проверяю, что контрольные суммы выключены:
    postgres@postgres2022:~$ psql -c "SHOW data_checksums;"
     data_checksums 
    ----------------
     off
    (1 row)

6.2. Останавливаю кластер

6.3. Включаю контрольные суммы:
    postgres@postgres2022:~$ /usr/lib/postgresql/14/bin/pg_checksums --enable -D "/var/lib/postgresql/14/main"
    Checksum operation completed
    Files scanned:  931
    Blocks scanned: 3216
    pg_checksums: syncing data directory
    pg_checksums: updating control file
    Checksums enabled in cluster

6.4. Запускаю кластер

6.5. Проверяю, что контрольные суммы включены
    postgres@postgres2022:~$ psql -c "SHOW data_checksums;"
     data_checksums 
    ----------------
     on
    (1 row)

6.6. Создаю таблицу и вставляю данные
    postgres=# create table test(c1 text);
    CREATE TABLE
    postgres=# insert into test values('1');
    INSERT 0 1
    postgres=# insert into test values('test1');
    INSERT 0 1

6.7. Определяю файл, в котором содержится созданная таблица
    postgres=# SELECT pg_relation_filepath('test');
     pg_relation_filepath 
    ----------------------
     base/13760/16384
    (1 row)

6.8. Останавливаю кластер

6.9. Вношу изменения в файл, запускаю кластер

6.10. Делаю выборку из кластера
    postgres=# select * from test;
    WARNING:  page verification failed, calculated checksum 14514 but expected 5565
    ERROR:  invalid page in block 0 of relation base/13760/16384

Текущая контрольная сумма страницы отличается от заданной

6.11. Включаю игнорирование ошибок контрольных сумм
    postgres=# alter system set ignore_checksum_failure = on;
    postgres=# SELECT pg_reload_conf();

6.12. Проверяю
    postgres=# show ignore_checksum_failure;
     ignore_checksum_failure 
    -------------------------
     on
    (1 row)

6.13. Делаю выборку
    postgres=# select * from test;
    WARNING:  page verification failed, calculated checksum 14514 but expected 5565
      c1   
    -------
     1
     test1
    (2 rows)
```
