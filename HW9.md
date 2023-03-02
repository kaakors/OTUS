>   На 1 ВМ создаем таблицы test для записи, test2 для запросов на чтение. 
>   Создаем публикацию таблицы test и подписываемся на публикацию таблицы test2 с ВМ №2. 
>   На 2 ВМ создаем таблицы test2 для записи, test для запросов на чтение. 
>   Создаем публикацию таблицы test2 и подписываемся на публикацию таблицы test1 с ВМ №1. 
>   3 ВМ использовать как реплику для чтения и бэкапов (подписаться на таблицы из ВМ №1 и №2 )


### Выставляем нужный уровень репликации:

>   ALTER SYSTEM SET wal_level=logical;
>   перезагружаем сервис postgresql для применения настроек

### Создаем тестовые таблицы:

>   CREATE TABLE test(id integer);  
>   CREATE TABLE test2(id integer); 

### Аналогично выполняем на 2ом и 3ем кластере

>   На 1 ВМ создаем публикацию таблицы test
>   На 2 ВМ создаем публикацию таблицы test2
>   
>   CREATE PUBLICATION testP FOR TABLE test;
>   CREATE PUBLICATION testP2 FOR TABLE test2;

### Подписываемся на 1м кластера на таблицу test2 кластера 2, на 2ом кластере на таблицу test1 кластера 1

>   1:
>   
>   CREATE SUBSCROPTION test2S  
>     CONNECTION 'host=127.0.0.1 port=5433 user=postgres password=postgres dbname=postgres'  
>     PUBLICATION testP2 WITH (copy_data=true);
>     
>   2:
>   
>   CREATE SUBSCROPTION testS  
>     CONNECTION 'host=127.0.0.1 port=5432 user=postgres password=postgres dbname=postgres'  
>     PUBLICATION testP WITH (copy_data=true);
  
  
### Подписываемся с 3го кластера на таблицы с кластера 1 и 2


>   CREATE SUBSCROPTION test3S1  
>     CONNECTION 'host=127.0.0.1 port=5433 user=postgres password=postgres dbname=postgres'  
>     PUBLICATION testP2 WITH (copy_data=true);
>     
>   CREATE SUBSCROPTION test3S2  
>     CONNECTION 'host=127.0.0.1 port=5433 user=postgres password=postgres dbname=postgres'  
>     PUBLICATION testP WITH (copy_data=true);
