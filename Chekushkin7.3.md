sudo pg_ctlcluster 16 main start
tmeo@Desktop:~$ psql
psql: error: connection to server on socket "/var/run/postgresql/.s.PGSQL.5432" failed: FATAL:  role "tmeo" does not exist
tmeo@Desktop:~$ psql -U postgres
psql (16.4 (Ubuntu 16.4-0ubuntu0.24.04.2))
Type "help" for help.

postgres=# CREATE DATABASE backup_base;
]CREATE DATABASE
postgres=# \c backup_base
You are now connected to database "backup_base" as user "postgres".
backup_base=# CREATE TABLE t(s text);
CREATE TABLE
backup_base=# INSERT INTO t VALUES ('Привет, мир!');
INSERT 0 1
tmeo@Desktop:~$ sudo head -n 1 /var/lib/postgresql/16/main/postmaster.pid
1550
tmeo@Desktop:~$ sudo kill -9 1550
tmeo@Desktop:~$ sudo pg_ctlcluster 16 main status
pg_ctl: no server running
tmeo@Desktop:~$ sudo rm -rf /var/lib/postgresql/16/beta
tmeo@Desktop:~$ sudo cp -rp /var/lib/postgresql/16/main /var/lib/postgresql/16/beta
tmeo@Desktop:~$ sudo pg_ctlcluster 16 main start
tmeo@Desktop:~$ sudo tail -n 5 /var/log/postgresql/postgresql-16-main.log
2024-12-05 17:01:39.163 MSK [4032] LOG:  invalid record length at 0/54B5E20: expected at least 24, got 0
2024-12-05 17:01:39.163 MSK [4032] LOG:  redo done at 0/54B5DE8 system usage: CPU: user: 0.00 s, system: 0.02 s, elapsed: 0.02 s
2024-12-05 17:01:39.173 MSK [4030] LOG:  checkpoint starting: end-of-recovery immediate wait
2024-12-05 17:01:39.225 MSK [4030] LOG:  checkpoint complete: wrote 938 buffers (5.7%); 0 WAL file(s) added, 0 removed, 0 recycled; write=0.044 s, sync=0.003 s, total=0.053 s; sync files=304, longest=0.001 s, average=0.001 s; distance=4311 kB, estimate=4311 kB; lsn=0/54B5E20, redo lsn=0/54B5E20
2024-12-05 17:01:39.233 MSK [4029] LOG:  database system is ready to accept connections
tmeo@Desktop:~$ psql -U postgres -d backup_base
psql (16.4 (Ubuntu 16.4-0ubuntu0.24.04.2))
Type "help" for help.

backup_base=# SELECT * FROM t;
      s       
--------------
 Привет, мир!
(1 row)

backup_base=# sudo pg_ctlcluster 13 main stop
backup_base-# \q
tmeo@Desktop:~$ sudo pg_ctlcluster 16 main stop

