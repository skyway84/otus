По-умолчанию
autovacuum                            | on        | sighup     | Starts the autovacuum subprocess.
 autovacuum_analyze_scale_factor       | 0.1       | sighup     | Number of tuple inserts, updates, or deletes prior to analyze as a fraction of reltuples.
 autovacuum_analyze_threshold          | 50        | sighup     | Minimum number of tuple inserts, updates, or deletes prior to analyze.
 autovacuum_freeze_max_age             | 200000000 | postmaster | Age at which to autovacuum a table to prevent transaction ID wraparound.
 autovacuum_max_workers                | 3         | postmaster | Sets the maximum number of simultaneously running autovacuum worker processes.
 autovacuum_multixact_freeze_max_age   | 400000000 | postmaster | Multixact age at which to autovacuum a table to prevent multixact wraparound.
 autovacuum_naptime                    | 60        | sighup     | Time to sleep between autovacuum runs.
 autovacuum_vacuum_cost_delay          | 2         | sighup     | Vacuum cost delay in milliseconds, for autovacuum.
 autovacuum_vacuum_cost_limit          | -1        | sighup     | Vacuum cost amount available before napping, for autovacuum.
 autovacuum_vacuum_insert_scale_factor | 0.2       | sighup     | Number of tuple inserts prior to vacuum as a fraction of reltuples.
 autovacuum_vacuum_insert_threshold    | 1000      | sighup     | Minimum number of tuple inserts prior to vacuum, or -1 to disable insert vacuums.
 autovacuum_vacuum_scale_factor        | 0.2       | sighup     | Number of tuple updates or deletes prior to vacuum as a fraction of reltuples.
 autovacuum_vacuum_threshold           | 50        | sighup     | Minimum number of tuple updates or deletes prior to vacuum.
 autovacuum_work_mem                   | -1        | sighup     | Sets the maximum memory to be used by each autovacuum worker process.

Конфигурация 1
autovacuum_vacuum_threshold = 500
autovacuum_vacuum_scale_factor = 0

alter table pgbench_branches set (autovacuum_vacuum_threshold = 500);
alter table pgbench_branches set (autovacuum_vacuum_scale_factor = 0);
alter table pgbench_tellers set (autovacuum_vacuum_threshold = 500);
alter table pgbench_tellers set (autovacuum_vacuum_scale_factor = 0);
alter table pgbench_accounts set (autovacuum_vacuum_threshold = 500);
alter table pgbench_accounts set (autovacuum_vacuum_scale_factor = 0);
alter table pgbench_history set (autovacuum_vacuum_threshold = 500);
alter table pgbench_history set (autovacuum_vacuum_scale_factor = 0);


Конфигурация 1.1
autovacuum_vacuum_threshold = 1000
autovacuum_vacuum_scale_factor = 0

alter table pgbench_branches set (autovacuum_vacuum_threshold = 1000);
alter table pgbench_branches set (autovacuum_vacuum_scale_factor = 0);
alter table pgbench_tellers set (autovacuum_vacuum_threshold = 1000);
alter table pgbench_tellers set (autovacuum_vacuum_scale_factor = 0);
alter table pgbench_accounts set (autovacuum_vacuum_threshold = 1000);
alter table pgbench_accounts set (autovacuum_vacuum_scale_factor = 0);
alter table pgbench_history set (autovacuum_vacuum_threshold = 1000);
alter table pgbench_history set (autovacuum_vacuum_scale_factor = 0);


Конфигурация 2
autovacuum_vacuum_threshold = 25
autovacuum_vacuum_scale_factor = 0

alter table pgbench_branches set (autovacuum_vacuum_threshold = 25);
alter table pgbench_branches set (autovacuum_vacuum_scale_factor = 0);
alter table pgbench_tellers set (autovacuum_vacuum_threshold = 25);
alter table pgbench_tellers set (autovacuum_vacuum_scale_factor = 0);
alter table pgbench_accounts set (autovacuum_vacuum_threshold = 25);
alter table pgbench_accounts set (autovacuum_vacuum_scale_factor = 0);
alter table pgbench_history set (autovacuum_vacuum_threshold = 25);
alter table pgbench_history set (autovacuum_vacuum_scale_factor = 0);

Конфигурация 3
autovacuum_vacuum_threshold = 10
autovacuum_vacuum_scale_factor = 0

alter table pgbench_branches set (autovacuum_vacuum_threshold = 10);
alter table pgbench_branches set (autovacuum_vacuum_scale_factor = 0);
alter table pgbench_tellers set (autovacuum_vacuum_threshold = 10);
alter table pgbench_tellers set (autovacuum_vacuum_scale_factor = 0);
alter table pgbench_accounts set (autovacuum_vacuum_threshold = 10);
alter table pgbench_accounts set (autovacuum_vacuum_scale_factor = 0);
alter table pgbench_history set (autovacuum_vacuum_threshold = 10);
alter table pgbench_history set (autovacuum_vacuum_scale_factor = 0);


Конфигурация 4
autovacuum_vacuum_threshold = 500
autovacuum_vacuum_scale_factor = 0.05

alter table pgbench_branches set (autovacuum_vacuum_threshold = 500);
alter table pgbench_branches set (autovacuum_vacuum_scale_factor = 0.05);
alter table pgbench_tellers set (autovacuum_vacuum_threshold = 500);
alter table pgbench_tellers set (autovacuum_vacuum_scale_factor = 0.05);
alter table pgbench_accounts set (autovacuum_vacuum_threshold = 500);
alter table pgbench_accounts set (autovacuum_vacuum_scale_factor = 0.05);
alter table pgbench_history set (autovacuum_vacuum_threshold = 500);
alter table pgbench_history set (autovacuum_vacuum_scale_factor = 0.05);


Конфигурация 5
autovacuum_vacuum_threshold = 1000
autovacuum_vacuum_scale_factor = 0

alter table pgbench_branches set (autovacuum_vacuum_threshold = 1000);
alter table pgbench_branches set (autovacuum_vacuum_scale_factor = 0);
alter table pgbench_tellers set (autovacuum_vacuum_threshold = 1000);
alter table pgbench_tellers set (autovacuum_vacuum_scale_factor = 0);
alter table pgbench_accounts set (autovacuum_vacuum_threshold = 1000);
alter table pgbench_accounts set (autovacuum_vacuum_scale_factor = 0);
alter table pgbench_history set (autovacuum_vacuum_threshold = 1000);
alter table pgbench_history set (autovacuum_vacuum_scale_factor = 0);


Конфигурация 6
autovacuum_vacuum_threshold = 100
autovacuum_vacuum_scale_factor = 0

alter table pgbench_branches set (autovacuum_vacuum_threshold = 100);
alter table pgbench_branches set (autovacuum_vacuum_scale_factor = 0);
alter table pgbench_tellers set (autovacuum_vacuum_threshold = 100);
alter table pgbench_tellers set (autovacuum_vacuum_scale_factor = 0);
alter table pgbench_accounts set (autovacuum_vacuum_threshold = 100);
alter table pgbench_accounts set (autovacuum_vacuum_scale_factor = 0);
alter table pgbench_history set (autovacuum_vacuum_threshold = 100);
alter table pgbench_history set (autovacuum_vacuum_scale_factor = 0);


Конфигурация 7
autovacuum_vacuum_threshold = 500
autovacuum_vacuum_scale_factor = 0
autovacuum_naptime = 1s

alter table pgbench_branches set (autovacuum_vacuum_threshold = 500);
alter table pgbench_branches set (autovacuum_vacuum_scale_factor = 0);
alter table pgbench_tellers set (autovacuum_vacuum_threshold = 500);
alter table pgbench_tellers set (autovacuum_vacuum_scale_factor = 0);
alter table pgbench_accounts set (autovacuum_vacuum_threshold = 500);
alter table pgbench_accounts set (autovacuum_vacuum_scale_factor = 0);
alter table pgbench_history set (autovacuum_vacuum_threshold = 500);
alter table pgbench_history set (autovacuum_vacuum_scale_factor = 0);




Конфигурация 4
autovacuum = off

Конфигурация 5
autovacuum_max_workers 10
autovacuum_vacuum_threshold = 50
autovacuum_analyze_threshold = 10
autovacuum_vacuum_scale_factor = 0.05
autovacuum_analyze_scale_factor = 0.05.
vacuum_freeze_min_age = 5000000
vacuum_freeze_table_age = 15000000

Конфигурация 6
autovacuum_analyze_threshold = '10'
autovacuum_vacuum_scale_factor = '0.05'
autovacuum_analyze_scale_factor = '0.05'
autovacuum_max_workers = '4'
autovacuum_vacuum_threshold = '25'

Конфигурация 7
autovacuum_vacuum_scale_factor = '0.05'
autovacuum_analyze_scale_factor = '0.05'
autovacuum_max_workers = '4'
autovacuum_vacuum_threshold = '100'
autovacuum_analyze_threshold = '100'






