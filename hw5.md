создать GCE инстанс типа e2-medium и диском 10GB

    создал в yandex cloud

установить на него PostgreSQL 14 с дефолтными настройками

    выполнено

применить параметры настройки PostgreSQL из прикрепленного к материалам занятия файла

    выполнено

выполнить pgbench -i postgres

    выполнено

запустить pgbench -c8 -P 60 -T 600 -U postgres postgres

    результат до настройки autovacuum:    
    
<img width="607" alt="Снимок экрана 2023-01-16 в 02 06 20" src="https://user-images.githubusercontent.com/99620296/212572506-a3d6ac71-8f09-47df-a06f-418e2cf3fdd4.png">

дать отработать до конца


дальше настроить autovacuum максимально эффективно

    настройки:    
    
<img width="898" alt="Снимок экрана 2023-01-16 в 02 07 35" src="https://user-images.githubusercontent.com/99620296/212572524-283829a1-9a4c-4bb2-8596-357049aa08e5.png">

построить график по получившимся значениям

    значения после настройки:
    
<img width="630" alt="Снимок экрана 2023-01-16 в 02 06 31" src="https://user-images.githubusercontent.com/99620296/212572542-a8adb9eb-54c7-4912-bb8f-131f0ff13beb.png">


так чтобы получить максимально ровное значение tps
