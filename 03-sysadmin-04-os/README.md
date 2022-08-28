# Домашнее задание к занятию "3.4. Операционные системы, лекция 2"

1. На лекции мы познакомились с [node_exporter](https://github.com/prometheus/node_exporter/releases). В демонстрации его исполняемый файл запускался в background. Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. Используя знания из лекции по systemd, создайте самостоятельно простой [unit-файл](https://www.freedesktop.org/software/systemd/man/systemd.service.html) для node_exporter:

    * поместите его в автозагрузку,
    * предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на `systemctl cat cron`),
    * удостоверьтесь, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.
---
**Решение:**  

Для создания юнита node_exported я воспользовался следующей командой:  
`systemctl edit --full --force node_exporter`    

Файл конфигурации юнита следующий:
```
[Unit]
Description=Prometheus exporter for hardware and OS metrics
Documentation=https://prometheus.io/docs/guides/node-exporter/
FailureAction=exit
AssertPathExists=/home/vagrant/node_exporter-1.3.1.linux-amd64

[Service]
EnvironmentFile=/home/vagrant/node_exporter-1.3.1.linux-amd64/node_exporter.conf
ExecStart=/home/vagrant/node_exporter-1.3.1.linux-amd64/node_exporter $EXTRA_OPTS

[Install]
WantedBy=multi-user.target
``` 

Для того, чтобы добавить юнит в автозагрузку выполнил команду  
`systemctl enable node_exporter`
---


2. Ознакомьтесь с опциями node_exporter и выводом `/metrics` по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.
---
**Решение:**  

Я думаю, что можно было бы использовать эти опции:  
```
# HELP go_memstats_frees_total Total number of frees.
# TYPE go_memstats_frees_total counter
go_memstats_frees_total 1531

# HELP node_cpu_seconds_total Seconds the CPUs spent in each mode.
# TYPE node_cpu_seconds_total counter
node_cpu_seconds_total{cpu="0",mode="idle"} 712.22
node_cpu_seconds_total{cpu="0",mode="iowait"} 0.37
node_cpu_seconds_total{cpu="0",mode="irq"} 0
node_cpu_seconds_total{cpu="0",mode="nice"} 0
node_cpu_seconds_total{cpu="0",mode="softirq"} 0.26
node_cpu_seconds_total{cpu="0",mode="steal"} 0
node_cpu_seconds_total{cpu="0",mode="system"} 4.75
node_cpu_seconds_total{cpu="0",mode="user"} 2.17
node_cpu_seconds_total{cpu="1",mode="idle"} 714.11
node_cpu_seconds_total{cpu="1",mode="iowait"} 0.46
node_cpu_seconds_total{cpu="1",mode="irq"} 0
node_cpu_seconds_total{cpu="1",mode="nice"} 0
node_cpu_seconds_total{cpu="1",mode="softirq"} 0.16
node_cpu_seconds_total{cpu="1",mode="steal"} 0
node_cpu_seconds_total{cpu="1",mode="system"} 3.18
node_cpu_seconds_total{cpu="1",mode="user"} 1.87

# HELP node_disk_info Info of /sys/block/<block_device>.
# TYPE node_disk_info gauge
node_disk_info{device="dm-0",major="253",minor="0"} 1
node_disk_info{device="sda",major="8",minor="0"} 1
# HELP node_disk_io_now The number of I/Os currently in progress.
# TYPE node_disk_io_now gauge
node_disk_io_now{device="dm-0"} 0
node_disk_io_now{device="sda"} 0
# HELP node_disk_io_time_seconds_total Total seconds spent doing I/Os.
# TYPE node_disk_io_time_seconds_total counter
node_disk_io_time_seconds_total{device="dm-0"} 3.684
node_disk_io_time_seconds_total{device="sda"} 3.7720000000000002

# HELP node_filefd_allocated File descriptor statistics: allocated.
# TYPE node_filefd_allocated gauge
node_filefd_allocated 992

# HELP node_filesystem_avail_bytes Filesystem space available to non-root users in bytes.
# TYPE node_filesystem_avail_bytes gauge
node_filesystem_avail_bytes{device="/dev/mapper/ubuntu--vg-ubuntu--lv",fstype="ext4",mountpoint="/"} >node_filesystem_avail_bytes{device="/dev/sda2",fstype="ext4",mountpoint="/boot"} 8.41621504e+08
node_filesystem_avail_bytes{device="tmpfs",fstype="tmpfs",mountpoint="/run"} 2.07327232e+08
node_filesystem_avail_bytes{device="tmpfs",fstype="tmpfs",mountpoint="/run/lock"} 5.24288e+06
node_filesystem_avail_bytes{device="tmpfs",fstype="tmpfs",mountpoint="/run/snapd/ns"} 2.07327232e+08
node_filesystem_avail_bytes{device="tmpfs",fstype="tmpfs",mountpoint="/run/user/1000"} 2.0840448e+08
node_filesystem_avail_bytes{device="vagrant",fstype="vboxsf",mountpoint="/vagrant"} 2.55395233792e+11

# HELP node_filesystem_free_bytes Filesystem free space in bytes.
# TYPE node_filesystem_free_bytes gauge
node_filesystem_free_bytes{device="/dev/mapper/ubuntu--vg-ubuntu--lv",fstype="ext4",mountpoint="/"} 2>
node_filesystem_free_bytes{device="/dev/sda2",fstype="ext4",mountpoint="/boot"} 9.12084992e+08
node_filesystem_free_bytes{device="tmpfs",fstype="tmpfs",mountpoint="/run"} 2.07327232e+08
node_filesystem_free_bytes{device="tmpfs",fstype="tmpfs",mountpoint="/run/lock"} 5.24288e+06
node_filesystem_free_bytes{device="tmpfs",fstype="tmpfs",mountpoint="/run/snapd/ns"} 2.07327232e+08
node_filesystem_free_bytes{device="tmpfs",fstype="tmpfs",mountpoint="/run/user/1000"} 2.0840448e+08
node_filesystem_free_bytes{device="vagrant",fstype="vboxsf",mountpoint="/vagrant"} 2.55395233792e+11

# HELP node_netstat_Tcp_ActiveOpens Statistic TcpActiveOpens.
# TYPE node_netstat_Tcp_ActiveOpens untyped
node_netstat_Tcp_ActiveOpens 3
# HELP node_netstat_Tcp_CurrEstab Statistic TcpCurrEstab.
# TYPE node_netstat_Tcp_CurrEstab untyped
node_netstat_Tcp_CurrEstab 3

# HELP node_network_carrier carrier value of /sys/class/net/<iface>.
# TYPE node_network_carrier gauge
node_network_carrier{device="eth0"} 1
node_network_carrier{device="lo"} 1

# HELP node_network_receive_bytes_total Network device statistic receive_bytes.
# TYPE node_network_receive_bytes_total counter
node_network_receive_bytes_total{device="eth0"} 91523
node_network_receive_bytes_total{device="lo"} 61402

# HELP node_network_transmit_bytes_total Network device statistic transmit_bytes.
# TYPE node_network_transmit_bytes_total counter
node_network_transmit_bytes_total{device="eth0"} 107049
node_network_transmit_bytes_total{device="lo"} 61402

# HELP node_os_info A metric with a constant '1' value labeled by build_id, id, id_like, image_id, im># TYPE node_os_info gauge
node_os_info{build_id="",id="ubuntu",id_like="debian",image_id="",image_version="",name="Ubuntu",pret># HELP node_os_version Metric containing the major.minor part of the OS version.
# TYPE node_os_version gauge
node_os_version{id="ubuntu",id_like="debian",name="Ubuntu"} 20.04
```

---
3. Установите в свою виртуальную машину [Netdata](https://github.com/netdata/netdata). Воспользуйтесь [готовыми пакетами](https://packagecloud.io/netdata/netdata/install) для установки (`sudo apt install -y netdata`). После успешной установки:
    * в конфигурационном файле `/etc/netdata/netdata.conf` в секции [web] замените значение с localhost на `bind to = 0.0.0.0`,
    * добавьте в Vagrantfile проброс порта Netdata на свой локальный компьютер и сделайте `vagrant reload`:

    ```bash
    config.vm.network "forwarded_port", guest: 19999, host: 19999
    ```

    После успешной перезагрузки в браузере *на своем ПК* (не в виртуальной машине) вы должны суметь зайти на `localhost:19999`. Ознакомьтесь с метриками, которые по умолчанию собираются Netdata и с комментариями, которые даны к этим метрикам.
---
**Решение:**  

Netdata предоставляет широкий спектр мониторинга по разным категориям.  Веб-страница разделена на секции:  
```
 System Overview
 CPUs
 Memory
 Disks
 Networking Stack
 IPv4 Networking
 IPv6 Networking
 Network Interfaces
 systemd Services
 Applications
 User Groups
 Users
 Netdata Monitoring
```

Внутри каждой секции можно увидеть графики и индикаторы, указывающие текущее состояние.  
Кроме того, в каждой секции есть подразделения по определённой группировке.  
Netdata позволяет сконфигурировать собираемую статистику и добавить новые графики.
---
4. Можно ли по выводу `dmesg` понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?
---
**Решение:** 

Да, например по строчке  
`Hypervisor detected: KVM`
---
5. Как настроен sysctl `fs.nr_open` на системе по-умолчанию? Узнайте, что означает этот параметр. Какой другой существующий лимит не позволит достичь такого числа (`ulimit --help`)?
---
**Решение:** 

`fs.nr_open` - это системное ограничение - максимальное количество открытых файловых дескрипторов.  
По умолчанию `fs.nr_open = 1048576`.  
Однако этот лимит не достижим при обычных условиях, поскольку существует другой лимит для текущей оболочки, который задаётся с помощью `ulimit` и он равен 1024:  
```
vagrant@vagrant:~$ ulimit -n -S
1024
```
---
6. Запустите любой долгоживущий процесс (не `ls`, который отработает мгновенно, а, например, `sleep 1h`) в отдельном неймспейсе процессов; покажите, что ваш процесс работает под PID 1 через `nsenter`. Для простоты работайте в данном задании под root (`sudo -i`). Под обычным пользователем требуются дополнительные опции (`--map-root-user`) и т.д.
---
**Решение:**  

В отдельной сессии я создал неймспейс:
```
root@vagrant:~# screen
root@vagrant:~# unshare -f --pid --mount-proc /bin/bash
root@vagrant:~# ps aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.1   7236  3844 pts/2    S    21:50   0:00 /bin/bash
root           8  0.0  0.1   8888  3316 pts/2    R+   21:50   0:00 ps aux
root@vagrant:~# ping google.com
PING google.com (142.250.179.142) 56(84) bytes of data.
64 bytes from ams17s10-in-f14.1e100.net (142.250.179.142): icmp_seq=1 ttl=117 time=74.7 ms
```

В другой сессии нашёл процесс с отдельным неймспейсом и "зашёл" в этот неймспейс:
```
root@vagrant:~# ps aux
...
root        1780  0.0  0.1   7236  3992 pts/2    Ss   21:45   0:00 /bin/bash
root        1787  0.0  0.0   5480   580 pts/2    S    21:45   0:00 unshare -f --pid --mount-proc /bin/
root        1788  0.0  0.1   7236  3980 pts/2    S    21:45   0:00 /bin/bash
root        1796  0.0  0.1   7204  2728 pts/2    S+   21:45   0:00 ping google.com
root        1809  0.0  0.0      0     0 ?        I    21:46   0:00 [kworker/u4:0-events_unbound]
root        1810  0.0  0.2   9260  4548 pts/0    S    21:46   0:00 sudo -i
root        1811  0.0  0.2   7236  4128 pts/0    S    21:46   0:00 -bash
root        1827  0.0  0.1   8888  3336 pts/0    R+   21:46   0:00 ps aux
root@vagrant:~# ps aux | grep ping
root        1828  0.0  0.1   7204  2788 pts/2    S+   21:47   0:00 ping google.com
root        1830  0.0  0.0   6300   656 pts/0    S+   21:47   0:00 grep --color=auto ping
root@vagrant:~# nsenter --target 1788 --pid --mount
root@vagrant:/# echo $$
11
root@vagrant:/# ps aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.1   7236  3980 pts/2    S    21:45   0:00 /bin/bash
root          10  0.0  0.1   7204  2788 pts/2    S+   21:47   0:00 ping google.com
root          11  0.0  0.1   7236  4048 pts/0    S    21:47   0:00 -bash
root          22  0.0  0.1   8888  3280 pts/0    R+   21:48   0:00 ps aux
root@vagrant:/#
```

---
7. Найдите информацию о том, что такое `:(){ :|:& };:`. Запустите эту команду в своей виртуальной машине Vagrant с Ubuntu 20.04 (**это важно, поведение в других ОС не проверялось**). Некоторое время все будет "плохо", после чего (минуты) – ОС должна стабилизироваться. Вызов `dmesg` расскажет, какой механизм помог автоматической стабилизации. Как настроен этот механизм по-умолчанию, и как изменить число процессов, которое можно создать в сессии?
---
**Решение:**  

Данную команду:
`:(){ :|:& };:`  
можно преобразовать, заменив : именем f. Тогда получится следующий код:  
```
f() {
  f | f &
}
f
```

Это функция, которая делает fork двух таких же функций, каждая их которых делает fork ещё по два раза и так далее. Это забивает оперативную память и создаёт огромное количетсво форков, поэтому система сильно нагружается.  
Согласно сообщению `dmesg`, дальнейшее распространение форков было остановлено контроллером пидов:  
`[ 1920.591049] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-3.scope`  
Восстановление системы было возможно благодаря механизму под названием cgroups или "control groups". Cgroups – это механизм ядра, позволяющий ограничивать использование, вести учет и изолировать потребление системных ресурсов.

---


## Как сдавать задания

Обязательными к выполнению являются задачи без указания звездочки. Их выполнение необходимо для получения зачета и диплома о профессиональной переподготовке.

Задачи со звездочкой (*) являются дополнительными задачами и/или задачами повышенной сложности. Они не являются обязательными к выполнению, но помогут вам глубже понять тему.

Домашнее задание выполните в файле readme.md в github репозитории. В личном кабинете отправьте на проверку ссылку на .md-файл в вашем репозитории.

Также вы можете выполнить задание в [Google Docs](https://docs.google.com/document/u/0/?tgif=d) и отправить в личном кабинете на проверку ссылку на ваш документ.
Название файла Google Docs должно содержать номер лекции и фамилию студента. Пример названия: "1.1. Введение в DevOps — Сусанна Алиева".

Если необходимо прикрепить дополнительные ссылки, просто добавьте их в свой Google Docs.

Перед тем как выслать ссылку, убедитесь, что ее содержимое не является приватным (открыто на комментирование всем, у кого есть ссылка), иначе преподаватель не сможет проверить работу. Чтобы это проверить, откройте ссылку в браузере в режиме инкогнито.

[Как предоставить доступ к файлам и папкам на Google Диске](https://support.google.com/docs/answer/2494822?hl=ru&co=GENIE.Platform%3DDesktop)

[Как запустить chrome в режиме инкогнито ](https://support.google.com/chrome/answer/95464?co=GENIE.Platform%3DDesktop&hl=ru)

[Как запустить  Safari в режиме инкогнито ](https://support.apple.com/ru-ru/guide/safari/ibrw1069/mac)

Любые вопросы по решению задач задавайте в чате Slack.

---
