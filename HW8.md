настроить кластер PostgreSQL 14 на максимальную производительность не
обращая внимание на возможные проблемы с надежностью в случае
аварийной перезагрузки виртуальной машины

    показатели до настройки:
    
<img width="705" alt="Снимок экрана 2023-02-06 в 12 19 06" src="https://user-images.githubusercontent.com/99620296/216936415-8fd1f472-af99-4d2a-8054-10227a542e8c.png">

    Выполнил настройки(настройки расчитал в pg_tune):
    
    ALTER SYSTEM SET max_connections = '200';
    ALTER SYSTEM SET shared_buffers = '1GB';
    ALTER SYSTEM SET effective_cache_size = '3GB';
    ALTER SYSTEM SET maintenance_work_mem = '256MB';
    ALTER SYSTEM SET checkpoint_completion_target = '0.9';
    ALTER SYSTEM SET wal_buffers = '16MB';
    ALTER SYSTEM SET default_statistics_target = '100';
    ALTER SYSTEM SET random_page_cost = '1.1';
    ALTER SYSTEM SET effective_io_concurrency = '200';
    ALTER SYSTEM SET work_mem = '2621kB';
    ALTER SYSTEM SET min_wal_size = '1GB';
    ALTER SYSTEM SET max_wal_size = '4GB';
    
    Также для максимального эффекта добавил:
    
    alter system set synchronous_commit = off;
    
    показатели после настройки:
    
<img width="691" alt="Снимок экрана 2023-02-06 в 12 36 51" src="https://user-images.githubusercontent.com/99620296/216937091-97660fc1-e7c8-4b16-9018-65f32fd60818.png">

    Значения значительно лучше первоначальных
