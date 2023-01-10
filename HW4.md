1 создайте новый кластер PostgresSQL 14

2 зайдите в созданный кластер под пользователем postgres

3 создайте новую базу данных testdb

    CREATE DATABASE testdb;

4 зайдите в созданную базу данных под пользователем postgres

    \c testdb

5 создайте новую схему testnm

    CREATE SCHEMA testnm;

6 создайте новую таблицу t1 с одной колонкой c1 типа integer

    CREATE TABLE t1(c1 integer);

7 вставьте строку со значением c1=1

    INSERT INTO t1(c1) values (1);

8 создайте новую роль readonly

    CREATE role readonly;

9 дайте новой роли право на подключение к базе данных testdb

    grant connect on DATABASE testdb TO readonly;

10 дайте новой роли право на использование схемы testnm

    grant usage on SCHEMA testnm TO readonly;

11 дайте новой роли право на select для всех таблиц схемы testnm

    grant SELECT on all TABLES in SCHEMA testnm TO readonly;

12 создайте пользователя testread с паролем test123

    CREATE USER testread with password 'test123';

13 дайте роль readonly пользователю testread

    grant readonly TO testread;

14 зайдите под пользователем testread в базу данных testdb

    \c testdb testread

15 сделайте select * from t1;

16 получилось? (могло если вы делали сами не по шпаргалке и не упустили один существенный момент про который позже)


17 напишите что именно произошло в тексте домашнего задания

    таблица создана в другом namespace, не хватает прав доступа

21 а почему так получилось с таблицей (если делали сами и без шпаргалки то может у вас все нормально)

    без явного указания в какой ns должна создаться таблица по умолчанию создается в public

22 вернитесь в базу данных testdb под пользователем postgres

23 удалите таблицу t1

    drop TABLE t1;

24 создайте ее заново но уже с явным указанием имени схемы testnm

    CREATE TABLE testnm.t1(c1 integer);

25 вставьте строку со значением c1=1

    INSERT INTO testnm.t1 values(1);

26 зайдите под пользователем testread в базу данных testdb

27 сделайте select * from testnm.t1;

28 получилось?

29 есть идеи почему? если нет - смотрите шпаргалку

    нед доступа, т.к. таблица t1 удалена из public

30 как сделать так чтобы такое больше не повторялось? если нет идей - смотрите шпаргалку

    дать доступ пользователю через ALTER PRIVILEGES

31 сделайте select * from testnm.t1;

32 получилось?

33 есть идеи почему? если нет - смотрите шпаргалку

31 сделайте select * from testnm.t1;

32 получилось?

    Да, после ALTER PRIVILEGES

33 ура!

34 теперь попробуйте выполнить команду create table t2(c1 integer); insert into t2 values (2);

35 а как так? нам же никто прав на создание таблиц и insert в них под ролью readonly?

    по умолчанию даются полные права на ns public

36 есть идеи как убрать эти права? если нет - смотрите шпаргалку

37 если вы справились сами то расскажите что сделали и почему, если смотрели шпаргалку - объясните что сделали и почему выполнив 
указанные в ней команды

    \c testdb postgres; 
    revoke CREATE on SCHEMA public FROM public; 
    revoke all on DATABASE testdb FROM public; 
    
    Данными командами производится отзыв прав на операции создания в ns public, и отзыв всех прав в таблице testdb в ns public

38 теперь попробуйте выполнить команду create table t3(c1 integer); insert into t2 values (2);

39 расскажите что получилось и почему
