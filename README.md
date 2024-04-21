### OTUS Linux Professional Lesson #10 | Subject: Инициализация системы. Systemd

#### ЦЕЛЬ:
1. Написать service, который будет раз в 30 секунд мониторить лог на предмет наличия ключевого слова (файл лога и ключевое слово должны задаваться в /etc/sysconfig или в /etc/default).
2. Установить spawn-fcgi и переписать init-скрипт на unit-файл (имя service должно называться так же: spawn-fcgi).
3. Дополнить unit-файл httpd (он же apache2) возможностью запустить несколько инстансов сервера с разными конфигурационными файлами.

#### ЗАДАНИЕ 1:
1.  Создаём файл с конфигурацией для сервиса в директории /etc/sysconfig - из неё сервис будет брать необходимые переменные
```
[root@nginx ~#] cat /etc/sysconfig/watchlog
# Configuration file for my watchlog service
# Place it to /etc/sysconfig

# File and word in that file that we will be monit
WORD="ALERT"
LOG=/var/log/watchlog.log
```
2. Cоздаем /var/log/watchlog.log и пишем туда строки на своё усмотрение и ключевое слово "ALERT"
```
[root@packages ~]# cat /var/log/watchlog.log
[INFO] Apr 21 07:40:01 packages systemd[1]: sysstat-collect.service: Succeeded.
[INFO] Apr 21 07:40:01 packages systemd[1]: Started system activity accounting tool.
[INFO] Apr 21 07:50:01 packages systemd[1]: Starting system activity accounting tool...
[INFO] Apr 21 07:50:01 packages systemd[1]: sysstat-collect.service: Succeeded.
[INFO] Apr 21 07:50:01 packages systemd[1]: Started system activity accounting tool.
[INFO] Apr 21 08:00:00 packages systemd[1]: Starting system activity accounting tool...
[INFO] Apr 21 08:00:00 packages systemd[1]: Started Update a database for mlocate.
[ALERT] Apr 21 08:00:00 packages systemd[1]: sysstat-collect.service: fail.
[INFO] Apr 21 08:00:00 packages systemd[1]: Started system activity accounting tool.
[INFO] Apr 21 08:00:01 packages systemd[1]: mlocate-updatedb.service: Succeeded.
[INFO] Apr 21 08:08:20 packages systemd[1]: Starting dnf makecache...
[INFO] Apr 21 08:08:20 packages dnf[10468]: Metadata cache refreshed recently.
[INFO] Apr 21 08:08:20 packages systemd[1]: dnf-makecache.service: Succeeded.
[INFO] Apr 21 08:08:20 packages systemd[1]: Started dnf makecache.
[INFO] Apr 21 08:09:26 packages su[10475]: (to root) vagrant on pts/0
[INFO] Apr 21 08:10:01 packages systemd[1]: Starting system activity accounting tool...
[ALERT] Apr 21 08:10:01 packages systemd[1]: sysstat-collect.service: fail.
[INFO] Apr 21 08:10:01 packages systemd[1]: Started system activity accounting tool.
```
3. Создаем скрипт, который будет отправлять лог в системный журнал когда найдет в нашем логе слово "ALERT":
```
[root@packages ~]# cat /opt/watchlog.sh
#!/bin/bash

WORD=$1
LOG=$2
DATE=`date`

if grep $WORD $LOG &> /dev/null; then
    logger "$DATE: I found word, Master!"
else
    exit 0
fi
```
```
[root@packages ~]# chmod +x /opt/watchlog.sh
```
4. Создаем юнит для сервиса:
```

```
