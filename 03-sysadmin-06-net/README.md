# Домашнее задание к занятию "3.6. Компьютерные сети, лекция 1"

1. Работа c HTTP через телнет.
- Подключитесь утилитой телнет к сайту stackoverflow.com
`telnet stackoverflow.com 80`
- отправьте HTTP запрос
```bash
GET /questions HTTP/1.0
HOST: stackoverflow.com
[press enter]
[press enter]
```
- В ответе укажите полученный HTTP код, что он означает?

#### Решение:
```
HTTP/1.1 301 Moved Permanently
Connection: close
Content-Length: 0
Server: Varnish
Retry-After: 0
Location: https://stackoverflow.com/questions
Accept-Ranges: bytes
Date: Sat, 08 Oct 2022 11:54:04 GMT
Via: 1.1 varnish
X-Served-By: cache-ams21076-AMS
X-Cache: HIT
X-Cache-Hits: 0
X-Timer: S1665230044.136768,VS0,VE0
Strict-Transport-Security: max-age=300
X-DNS-Prefetch-Control: off
```
Полученный код 301 означает, что тот ресурс, который мы пытаемся получить по ссылке, был навсегда перемещён в новое место, поэтому стоит обновить все ссылки на него на новые.

2. Повторите задание 1 в браузере, используя консоль разработчика F12.
- откройте вкладку `Network`
- отправьте запрос http://stackoverflow.com
- найдите первый ответ HTTP сервера, откройте вкладку `Headers`
- укажите в ответе полученный HTTP код.
- проверьте время загрузки страницы, какой запрос обрабатывался дольше всего?
- приложите скриншот консоли браузера в ответ.
#### Решение:
HTTP код, полученный в первом ответе от сервера `Status Code: 301 Moved Permanently`
Дольше всего обрабатывалась загрузка самого кода страницы  
![Chrome Developer Console](https://github.com/dmitriy-dronov-550503/sysadm-homeworks/blob/devsys10/03-sysadmin-06-net/DevConsole.PNG?raw=true)  

3. Какой IP адрес у вас в интернете?
#### Решение:
Динамический  
109.252.164.203  

4. Какому провайдеру принадлежит ваш IP адрес? Какой автономной системе AS? Воспользуйтесь утилитой `whois`
#### Решение:
Провайдер: Moscow Local Telephone Network (OAO MGTS)  
AS: AS25513  
```
vagrant@vagrant:~$ whois -h whois.radb.net 109.252.164.203
route:          109.252.0.0/16
descr:          Moscow Local Telephone Network (OAO MGTS)
descr:          Moscow, Russia
origin:         AS25513
mnt-by:         MGTS-USPD-MNT
created:        2010-06-30T16:15:54Z
last-modified:  2010-06-30T16:15:54Z
source:         RIPE
```

5. Через какие сети проходит пакет, отправленный с вашего компьютера на адрес 8.8.8.8? Через какие AS? Воспользуйтесь утилитой `traceroute`
#### Решение:
```
traceroute -An 8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  192.168.1.1 [*]  0.505 ms  0.488 ms  0.467 ms
 2  100.103.0.1 [*]  3.571 ms  3.764 ms  3.654 ms
 3  212.188.1.6 [AS8359]  5.216 ms  5.109 ms  3.975 ms
 4  * * *
 5  72.14.223.74 [AS15169]  4.835 ms 72.14.223.72 [AS15169]  4.827 ms 72.14.223.74 [AS15169]  4.710 ms
 6  108.170.250.51 [AS15169]  4.797 ms 108.170.250.130 [AS15169]  4.174 ms 108.170.250.34 [AS15169]  4.438 ms
 7  142.251.237.154 [AS15169]  19.206 ms 142.250.238.138 [AS15169]  16.207 ms 142.250.239.64 [AS15169]  19.087 ms
 8  66.249.95.224 [AS15169]  21.200 ms  25.616 ms 108.170.235.204 [AS15169]  19.138 ms
 9  142.250.238.179 [AS15169]  20.881 ms 172.253.51.223 [AS15169]  18.277 ms 142.250.236.77 [AS15169]  18.637 ms
10  * * *
11  * * *
12  * * *
13  * * *
14  * * *
15  * * *
16  * * *
17  * * *
18  * * *
19  8.8.8.8 [AS15169]  19.060 ms  17.878 ms *
```

6. Повторите задание 5 в утилите `mtr`. На каком участке наибольшая задержка - delay?  
#### Решение:
Наибольшая задержка наблюдается на участке `AS15169  66.249.95.224`
```
                                                          My traceroute  [v0.92]
MAIN (192.168.1.27)                                                                                       2022-10-08T15:22:36+0300
Keys:  Help   Display mode   Restart statistics   Order of fields   quit
                                                                                                 Packets               Pings
 Host                                                                                          Loss%   Snt   Last   Avg  Best  Wrst StDev
 1. AS???    192.168.1.1                                                                        0.0%   136    0.3   0.3   0.3   0.6   0.1
 2. AS???    100.103.0.1                                                                        0.0%   136   13.2   4.6   2.7  22.0   3.1
 3. AS8359   212.188.1.6                                                                        0.0%   136    3.1   5.6   3.1  19.4   3.4
 4. AS8359   212.188.1.5                                                                       88.9%   136    3.3   7.5   3.2  34.7  10.9
 5. AS15169  72.14.223.74                                                                       0.0%   136    4.6   4.5   3.3  47.0   3.7
 6. AS15169  108.170.250.113                                                                    0.7%   136    3.6   4.8   3.4  19.9   2.9
 7. AS15169  72.14.234.20                                                                       0.0%   136   17.9  20.2  16.3  49.0   7.3
 8. AS15169  66.249.95.224                                                                      0.0%   136   18.4  21.3  18.2  78.3   7.4
 9. AS15169  172.253.70.47                                                                      0.0%   136   16.2  16.3  15.7  25.3   0.8
10. ???
11. ???
12. ???
13. ???
14. ???
15. ???
16. ???
17. ???
18. ???
19. AS15169  8.8.8.8                                                                            0.0%   135   15.2  15.1  14.4  15.4   0.1
```

7. Какие DNS сервера отвечают за доменное имя dns.google? Какие A записи? воспользуйтесь утилитой `dig`
#### Решение:
За доменное имя dns.google отвечают следующие сервера:  
`dns.google.             6       IN      A       8.8.4.4`  
`dns.google.             6       IN      A       8.8.8.8`  
```
$ dig dns.google

; <<>> DiG 9.11.3-1ubuntu1.11-Ubuntu <<>> dns.google
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 55695
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;dns.google.                    IN      A

;; ANSWER SECTION:
dns.google.             6       IN      A       8.8.4.4
dns.google.             6       IN      A       8.8.8.8

;; Query time: 3 msec
;; SERVER: 192.168.1.1#53(192.168.1.1)
;; WHEN: Sat Oct 08 15:28:11 +03 2022
;; MSG SIZE  rcvd: 71

```
8. Проверьте PTR записи для IP адресов из задания 7. Какое доменное имя привязано к IP? воспользуйтесь утилитой `dig`
#### Решение:
```
$ dig -x 8.8.4.4

; <<>> DiG 9.11.3-1ubuntu1.11-Ubuntu <<>> -x 8.8.4.4
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 30495
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;4.4.8.8.in-addr.arpa.          IN      PTR

;; ANSWER SECTION:
4.4.8.8.in-addr.arpa.   79159   IN      PTR     dns.google.

;; Query time: 3 msec
;; SERVER: 192.168.1.1#53(192.168.1.1)
;; WHEN: Sat Oct 08 18:41:10 +03 2022
;; MSG SIZE  rcvd: 73

$ dig -x 8.8.8.8

; <<>> DiG 9.11.3-1ubuntu1.11-Ubuntu <<>> -x 8.8.8.8
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 7394
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;8.8.8.8.in-addr.arpa.          IN      PTR

;; ANSWER SECTION:
8.8.8.8.in-addr.arpa.   78529   IN      PTR     dns.google.

;; Query time: 3 msec
;; SERVER: 192.168.1.1#53(192.168.1.1)
;; WHEN: Sat Oct 08 18:41:46 +03 2022
;; MSG SIZE  rcvd: 73
```


