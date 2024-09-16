## tools

+ psql
+ Navicat


## Hardware

> apt-get install postgresql postgresql-client postgresql-contrib
> su postgres
> psql

>  search discountasp on github

> createdb scifi


## Locking things down

### Disable root login,create new user as sudo user

> create role boss with SUPERUSER CREATEDB CREATEROLE LOGIN;
> create role spock with login password '123456';
> alter database scifi owner to spock;

## Backup/Restore

> pg_dump -d scifi -f scifi.sql
> psql scifi < scifi.sql

> cron

https://txcowboycoder.wordpress.com/2011/06/03/automatic-cron-backup-of-postgresql-database/

> 0 0 * * * boss pg_dump -Fc scifi > backups/back.bk

## Benchmarking

> pgbench 
> createdb bench
> pgbench -i bench
> pgbench -i -s 100 bench 
> pgbench bench
> pgbench -T 120 -j 2 -c 20 -S bench # job nums equals to num of cores, run three times
> pgbench -T 120 -j 2 -c 20 bench 



## tweaking configuration

> psql scifi -c 'show config_file'
> /etc/postgresql/15/main/postgresql.conf
> psql scifi -c 'show shared_buffers';
> psql scifi -c 'show max_connections';

add this to the postgresql.conf

include postgresql.local.conf

> https://pgtune.leopard.in.ua/

## Introspection

https://www.craigkerstiens.com/2012/10/01/understanding-postgres-performance/


## Slow Query fixes

https://www.postgresql.org/docs/current/pgstatstatements.html


```sql
SELECT * 
FROM pg_available_extensions 
WHERE 
    name = 'pg_stat_statements' and 
    installed_version is not null;

```
> CREATE EXTENSION pg_stat_statements
> systemctl  restart  postgresql

> vim /etc/postgresql/15/main/postgresql.conf
> shared_preload_libraries = 'pg_stat_statements' 
> select pg_stat_reset();

```sql
SELECT 
  sum(heap_blks_read) as heap_read,
  sum(heap_blks_hit)  as heap_hit,
  sum(heap_blks_hit) / (sum(heap_blks_hit) + sum(heap_blks_read)) as ratio
FROM 
  pg_statio_user_tables;

```

> pg_warmup



## Text Query Optmization
