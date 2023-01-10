создайте виртуальную машину c Ubuntu 20.04 LTS (bionic) в GCE/ЯО
поставьте на нее PostgreSQL 14 через sudo apt
проверьте что кластер запущен через sudo -u postgres pg_lsclusters

   <img width="168" alt="Снимок экрана 2022-12-11 в 21 44 11" src="https://user-images.githubusercontent.com/99620296/211612374-55dc6adb-835d-4c96-ba83-d025e0664b13.png">

зайдите из под пользователя postgres в psql и сделайте произвольную таблицу с произвольным содержимым
postgres=# create table test(c1 text);
postgres=# insert into test values('1');
\q
остановите postgres например через sudo -u postgres pg_ctlcluster 14 main stop
создайте новый standard persistent диск GKE через Compute Engine -> Disks в том же регионе и зоне что GCE инстанс размером например 10GB
добавьте свеже-созданный диск к виртуальной машине - надо зайти в режим ее редактирования и дальше выбрать пункт attach existing disk
проинициализируйте диск согласно инструкции и подмонтировать файловую систему, только не забывайте менять имя диска на актуальное, в вашем случае это скорее всего будет /dev/sdb - https://www.digitalocean.com/community/tutorials/how-to-partition-and-format-storage-devices-in-linux
перезагрузите инстанс и убедитесь, что диск остается примонтированным (если не так смотрим в сторону fstab)
сделайте пользователя postgres владельцем /mnt/data - chown -R postgres:postgres /mnt/data/
перенесите содержимое /var/lib/postgres/14 в /mnt/data - mv /var/lib/postgresql/14 /mnt/data
попытайтесь запустить кластер - sudo -u postgres pg_ctlcluster 14 main start
напишите получилось или нет и почему

    Не получилось, т.к. мы перенесли данные куда обращается сервер по умолчанию

задание: найти конфигурационный параметр в файлах раположенных в /etc/postgresql/14/main который надо поменять и поменяйте его
напишите что и почему поменяли

    Изменил параметр PGDATA

попытайтесь запустить кластер - sudo -u postgres pg_ctlcluster 14 main start
напишите получилось или нет и почему

    Запустить получилось:

   <img width="705" alt="Снимок экрана 2022-12-11 в 21 44 30" src="https://user-images.githubusercontent.com/99620296/211613294-58f13ab3-6345-439d-83a8-86d22678f739.png">

зайдите через через psql и проверьте содержимое ранее созданной таблицы

   <img width="450" alt="Снимок экрана 2022-12-11 в 21 44 37" src="https://user-images.githubusercontent.com/99620296/211614008-7fbebe04-0f60-4f5b-befd-591d13d6b9aa.png">

