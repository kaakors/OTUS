1) Настройте сервер так, чтобы в журнал сообщений сбрасывалась информация о блокировках, удерживаемых более 200 миллисекунд. Воспроизведите ситуацию, при которой в журнале появятся такие сообщения.

    - ALTER SYSTEM SET log_lock_waits = on;
    - ALTER SYSTEM SET deadlock_timeout = 200;
    - далее выполняем перезапуск службы postgresql:
      systemctl restart postgresql-14

2) Смоделируйте ситуацию обновления одной и той же строки тремя командами UPDATE в разных сеансах. Изучите возникшие блокировки в представлении pg_locks и убедитесь, что все они понятны. Пришлите список блокировок и объясните, что значит каждая.

Создаем таблицу для тестов:

CREATE TABLE test (name varchar(100));
INSERT INTO test(name) values ('test');
INSERT INTO test(name) values ('test1');
INSERT INTO test(name) values ('test2');
INSERT INTO test(name) values ('test3');


select * from test;
 name  
-------
 test
 test1
 test2
 test3
(4 rows)

-----в первой сессии:
postgres=# begin;
BEGIN
postgres=*# UPDATE test SET name = 'test_1' WHERE name = 'test';
UPDATE 1

-----во второй сессии:
postgres=# UPDATE test SET name = 'test_2' WHERE name = 'test';

2023-02-19 18:32:56.942 UTC [8350] LOG:  process 8350 still waiting for ShareLock on transaction 739 after 200.206 ms
2023-02-19 18:32:56.942 UTC [8350] DETAIL:  Process holding the lock: 8314. Wait queue: 8350.
2023-02-19 18:32:56.942 UTC [8350] CONTEXT:  while updating tuple (0,1) in relation "test"
2023-02-19 18:32:56.942 UTC [8350] STATEMENT:  UPDATE test SET name = 'test_2' where name = 'test';

-----в третьей сессии добавил:



-----в четвертой сессии:
SELECT * FROM pg_locks \gx

-[ RECORD 1 ]------+------------------------------
locktype           | relation
database           | 14486
relation           | 12290
page               | 
tuple              | 
virtualxid         | 
transactionid      | 
classid            | 
objid              | 
objsubid           | 
virtualtransaction | 6/16
pid                | 8448
mode               | AccessShareLock
granted            | t
fastpath           | t
waitstart          | 
-[ RECORD 2 ]------+------------------------------
locktype           | virtualxid
database           | 
relation           | 
page               | 
tuple              | 
virtualxid         | 6/16
transactionid      | 
classid            | 
objid              | 
objsubid           | 
virtualtransaction | 6/16
pid                | 8448
mode               | ExclusiveLock
granted            | t
fastpath           | t
waitstart          | 
-[ RECORD 3 ]------+------------------------------
locktype           | relation
database           | 14486
relation           | 16384
page               | 
tuple              | 
virtualxid         | 
transactionid      | 
classid            | 
objid              | 
objsubid           | 
virtualtransaction | 5/29
pid                | 8417
mode               | RowExclusiveLock
granted            | t
fastpath           | t
waitstart          | 
-[ RECORD 4 ]------+------------------------------
locktype           | virtualxid
database           | 
relation           | 
page               | 
tuple              | 
virtualxid         | 5/29
transactionid      | 
classid            | 
objid              | 
objsubid           | 
virtualtransaction | 5/29
pid                | 8417
mode               | ExclusiveLock
granted            | t
fastpath           | t
waitstart          | 
-[ RECORD 5 ]------+------------------------------
locktype           | relation
database           | 14486
relation           | 16384
page               | 
tuple              | 
virtualxid         | 
transactionid      | 
classid            | 
objid              | 
objsubid           | 
virtualtransaction | 4/15
pid                | 8350
mode               | RowExclusiveLock
granted            | t
fastpath           | t
waitstart          | 
-[ RECORD 6 ]------+------------------------------
locktype           | virtualxid
database           | 
relation           | 
page               | 
tuple              | 
virtualxid         | 4/15
transactionid      | 
classid            | 
objid              | 
objsubid           | 
virtualtransaction | 4/15
pid                | 8350
mode               | ExclusiveLock
granted            | t
fastpath           | t
waitstart          | 
-[ RECORD 7 ]------+------------------------------
locktype           | relation
database           | 14486
relation           | 16384
page               | 
tuple              | 
virtualxid         | 
transactionid      | 
classid            | 
objid              | 
objsubid           | 
virtualtransaction | 3/14
pid                | 8314
mode               | RowExclusiveLock
granted            | t
fastpath           | t
waitstart          | 
-[ RECORD 8 ]------+------------------------------
locktype           | virtualxid
database           | 
relation           | 
page               | 
tuple              | 
virtualxid         | 3/14
transactionid      | 
classid            | 
objid              | 
objsubid           | 
virtualtransaction | 3/14
pid                | 8314
mode               | ExclusiveLock
granted            | t
fastpath           | t
waitstart          | 
-[ RECORD 9 ]------+------------------------------
locktype           | transactionid
database           | 
relation           | 
page               | 
tuple              | 
virtualxid         | 
transactionid      | 739
classid            | 
objid              | 
objsubid           | 
virtualtransaction | 4/15
pid                | 8350
mode               | ShareLock
granted            | f
fastpath           | f
waitstart          | 2023-02-19 18:32:56.742356+00
-[ RECORD 10 ]-----+------------------------------
locktype           | transactionid
database           | 
relation           | 
page               | 
tuple              | 
virtualxid         | 
transactionid      | 740
classid            | 
objid              | 
objsubid           | 
virtualtransaction | 4/15
pid                | 8350
mode               | ExclusiveLock
granted            | t
fastpath           | f
waitstart          | 
-[ RECORD 11 ]-----+------------------------------
locktype           | tuple
database           | 14486
relation           | 16384
page               | 0
tuple              | 1
virtualxid         | 
transactionid      | 
classid            | 
objid              | 
objsubid           | 
virtualtransaction | 5/29
pid                | 8417
mode               | ExclusiveLock
granted            | f
fastpath           | f
waitstart          | 2023-02-19 18:41:36.134586+00
-[ RECORD 12 ]-----+------------------------------
locktype           | transactionid
database           | 
relation           | 
page               | 
tuple              | 
virtualxid         | 
transactionid      | 741
classid            | 
objid              | 
objsubid           | 
virtualtransaction | 5/29
pid                | 8417
mode               | ExclusiveLock
granted            | t
fastpath           | f
waitstart          | 
-[ RECORD 13 ]-----+------------------------------
locktype           | tuple
database           | 14486
relation           | 16384
page               | 0
tuple              | 1
virtualxid         | 
transactionid      | 
classid            | 
objid              | 
objsubid           | 
virtualtransaction | 4/15
pid                | 8350
mode               | ExclusiveLock
granted            | t
fastpath           | f
waitstart          | 
-[ RECORD 14 ]-----+------------------------------
locktype           | transactionid
database           | 
relation           | 
page               | 
tuple              | 
virtualxid         | 
transactionid      | 739
classid            | 
objid              | 
objsubid           | 
virtualtransaction | 3/14
pid                | 8314
mode               | ExclusiveLock
granted            | t
fastpath           | f
waitstart          | 



3) Воспроизведите взаимоблокировку трех транзакций. Можно ли разобраться в ситуации постфактум, изучая журнал сообщений?

--------в первой сессии:
postgres=# BEGIN;
BEGIN
postgres=*# UPDATE test SET name = 'test_0' WHERE name = 'test';
UPDATE 1
postgres=*# UPDATE test SET name = 'test_3' WHERE name = 'test1';

---------во второй сессии:
postgres=# BEGIN;
BEGIN
postgres=*# UPDATE test SET name = 'test_1' WHERE name = 'test1';
UPDATE 1
postgres=*# UPDATE test SET name = 'test_4' WHERE name = 'test2';

----------в третьей сесии:
postgres=# BEGIN;
BEGIN
postgres=*# UPDATE test SET name = 'test_2' WHERE name = 'test2';
UPDATE 1
postgres=*# UPDATE test SET name = 'test_5' WHERE name = 'test';
ERROR:  deadlock detected
ПОДРОБНОСТИ:  Process 8621 waits for ShareLock on transaction 746; blocked by process 8629.
Process 8629 waits for ShareLock on transaction 747; blocked by process 8623.
Process 8623 waits for ShareLock on transaction 748; blocked by process 8621.
ПОДСКАЗКА:  See server log for query details.
КОНТЕКСТ:  while updating tuple (0,7) in relation "test"


----------смотрим логи сервера

2023-02-19 19:10:54.937 UTC [8629] DETAIL:  Process holding the lock: 8623. Wait queue: 8629.
2023-02-19 19:10:54.937 UTC [8629] CONTEXT:  while updating tuple (0,2) in relation "test"
2023-02-19 19:10:54.937 UTC [8629] STATEMENT:  UPDATE test SET name = 'test_3' WHERE name = 'test1';
2023-02-19 19:11:47.923 UTC [8623] LOG:  process 8623 still waiting for ShareLock on transaction 748 after 200.128 ms
2023-02-19 19:11:47.923 UTC [8623] DETAIL:  Process holding the lock: 8621. Wait queue: 8623.
2023-02-19 19:11:47.923 UTC [8623] CONTEXT:  while updating tuple (0,3) in relation "test"
2023-02-19 19:11:47.923 UTC [8623] STATEMENT:  UPDATE test SET name = 'test_4' WHERE name = 'test2';
2023-02-19 19:11:59.613 UTC [8621] LOG:  process 8621 detected deadlock while waiting for ShareLock on transaction 746 after 200.180 ms
2023-02-19 19:11:59.613 UTC [8621] DETAIL:  Process holding the lock: 8629. Wait queue: .
2023-02-19 19:11:59.613 UTC [8621] CONTEXT:  while updating tuple (0,7) in relation "test"
2023-02-19 19:11:59.613 UTC [8621] STATEMENT:  UPDATE test SET name = 'test_5' WHERE name = 'test';
2023-02-19 19:11:59.614 UTC [8621] ERROR:  deadlock detected
2023-02-19 19:11:59.614 UTC [8621] DETAIL:  Process 8621 waits for ShareLock on transaction 746; blocked by process 8629.
	Process 8629 waits for ShareLock on transaction 747; blocked by process 8623.
	Process 8623 waits for ShareLock on transaction 748; blocked by process 8621.
	Process 8621: UPDATE test SET name = 'test_5' WHERE name = 'test';
	Process 8629: UPDATE test SET name = 'test_3' WHERE name = 'test1';
	Process 8623: UPDATE test SET name = 'test_4' WHERE name = 'test2';
2023-02-19 19:11:59.614 UTC [8621] HINT:  See server log for query details.
2023-02-19 19:11:59.614 UTC [8621] CONTEXT:  while updating tuple (0,7) in relation "test"
2023-02-19 19:11:59.614 UTC [8621] STATEMENT:  UPDATE test SET name = 'test_5' WHERE name = 'test';
2023-02-19 19:11:59.614 UTC [8623] LOG:  process 8623 acquired ShareLock on transaction 748 after 11890.871 ms
2023-02-19 19:11:59.614 UTC [8623] CONTEXT:  while updating tuple (0,3) in relation "test"
2023-02-19 19:11:59.614 UTC [8623] STATEMENT:  UPDATE test SET name = 'test_4' WHERE name = 'test2';

Вывод:

Да, по логам можно разобрать в какой момент и из-за каких действий произошла блокировка


4) Могут ли две транзакции, выполняющие единственную команду UPDATE одной и той же таблицы (без where), заблокировать друг друга?

    нет, такая ситуация невозможна
