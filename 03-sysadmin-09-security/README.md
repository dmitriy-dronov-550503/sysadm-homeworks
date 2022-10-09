# Домашнее задание к занятию "3.9. Элементы безопасности информационных систем"

1. Установите Bitwarden плагин для браузера. Зарегестрируйтесь и сохраните несколько паролей.
#### Решение:
![Bitwarden Chrome Extension](https://github.com/dmitriy-dronov-550503/sysadm-homeworks/blob/devsys10/03-sysadmin-09-security/BitWarden.PNG?raw=true)  

2. Установите Google authenticator на мобильный телефон. Настройте вход в Bitwarden акаунт через Google authenticator OTP.
#### Решение:
![Bitwarden OTP](https://github.com/dmitriy-dronov-550503/sysadm-homeworks/blob/devsys10/03-sysadmin-09-security/OTP.jpeg?raw=true)  

3. Установите apache2, сгенерируйте самоподписанный сертификат, настройте тестовый сайт для работы по HTTPS.
#### Решение:
```
vagrant@vagrant:~$ sudo a2enmod ssl
Considering dependency setenvif for ssl:
Module setenvif already enabled
Considering dependency mime for ssl:
Module mime already enabled
Considering dependency socache_shmcb for ssl:
Enabling module socache_shmcb.
Enabling module ssl.
See /usr/share/doc/apache2/README.Debian.gz on how to configure SSL and create self-signed certificates.
To activate the new configuration, you need to run:
  systemctl restart apache2
vagrant@vagrant:~$ sudo systemctl restart apache2
vagrant@vagrant:~$ sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
> -keyout /etc/ssl/private/apache-selfsigned.key \
> -out /etc/ssl/certs/apache-selfsigned.crt \
> -subj "/C=RU/ST=Moscow/L=Moscow/O=Company Name/OU=Org/CN=www.example.com"
Generating a RSA private key
.............................................+++++
......+++++
writing new private key to '/etc/ssl/private/apache-selfsigned.key'
-----
vagrant@vagrant:~$ sudo nano /etc/apache2/sites-available/example.com.conf
<VirtualHost *:443>
        ServerName example.com
        DocumentRoot /var/www/example.com

        SSLEngine on
        SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
        SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key
</VirtualHost>
vagrant@vagrant:~$ sudo mkdir /var/www/example.com
vagrant@vagrant:~$ sudo nano /var/www/example.com/index.html
<h1>It worked!</h1>
vagrant@vagrant:~$ sudo a2ensite example.com.conf
Enabling site example.com.
To activate the new configuration, you need to run:
  systemctl reload apache2
vagrant@vagrant:~$ sudo apache2ctl configtest
AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerName' directive globally to suppress this message
Syntax OK
vagrant@vagrant:~$ sudo systemctl reload apache2
vagrant@vagrant:~$ curl https://127.0.0.1
curl: (60) SSL certificate problem: self signed certificate
More details here: https://curl.haxx.se/docs/sslcerts.html

curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it. To learn more about this situation and
how to fix it, please visit the web page mentioned above.
vagrant@vagrant:~$ curl https://127.0.0.1 -k
<h1>It worked!</h1>
```

4. Проверьте на TLS уязвимости произвольный сайт в интернете (кроме сайтов МВД, ФСБ, МинОбр, НацБанк, РосКосмос, РосАтом, РосНАНО и любых госкомпаний, объектов КИИ, ВПК ... и тому подобное).
#### Решение:
```
vagrant@vagrant:~/testssl.sh$ ./testssl.sh -U --sneaky https://www.netology.ru/

###########################################################
    testssl.sh       3.2rc2 from https://testssl.sh/dev/
    (0ed2bf0 2022-10-07 16:48:06)

      This program is free software. Distribution and
             modification under GPLv2 permitted.
      USAGE w/o ANY WARRANTY. USE IT AT YOUR OWN RISK!

       Please file bugs @ https://testssl.sh/bugs/

###########################################################

 Using "OpenSSL 1.0.2-bad (1.0.2k-dev)" [~183 ciphers]
 on vagrant:./bin/openssl.Linux.x86_64
 (built: "Sep  1 14:03:44 2022", platform: "linux-x86_64")


Testing all IPv4 addresses (port 443): 172.67.21.207 104.22.41.171 104.22.40.171
--------------------------------------------------------------------------------------------
 Start 2022-10-09 20:57:57        -->> 172.67.21.207:443 (www.netology.ru) <<--

 Further IP addresses:   104.22.40.171 104.22.41.171 2606:4700:10::6816:29ab 2606:4700:10::ac43:15cf 2606:4700:10::6816:28ab
 rDNS (172.67.21.207):   --
 Service detected:       HTTP


 Testing vulnerabilities

 Heartbleed (CVE-2014-0160)                not vulnerable (OK), no heartbeat extension
 CCS (CVE-2014-0224)                       not vulnerable (OK)
 Ticketbleed (CVE-2016-9244), experiment.  not vulnerable (OK), no session tickets
 ROBOT                                     not vulnerable (OK)
 Secure Renegotiation (RFC 5746)           OpenSSL handshake didn't succeed
 Secure Client-Initiated Renegotiation     not vulnerable (OK)
 CRIME, TLS (CVE-2012-4929)                not vulnerable (OK)
 BREACH (CVE-2013-3587)                    no gzip/deflate/compress/br HTTP compression (OK)  - only supplied "/" tested
 POODLE, SSL (CVE-2014-3566)               not vulnerable (OK)
 TLS_FALLBACK_SCSV (RFC 7507)              Downgrade attack prevention supported (OK)
 SWEET32 (CVE-2016-2183, CVE-2016-6329)    VULNERABLE, uses 64 bit block ciphers
 FREAK (CVE-2015-0204)                     not vulnerable (OK)
 DROWN (CVE-2016-0800, CVE-2016-0703)      not vulnerable on this host and port (OK)
                                           make sure you don't use this certificate elsewhere with SSLv2 enabled services, see
                                           https://search.censys.io/search?resource=hosts&virtual_hosts=INCLUDE&q=A3C7D9A8D3805171D99EA61F5C80B8ADF49B93BA21EBB492D78512BA254E90A5
 LOGJAM (CVE-2015-4000), experimental      not vulnerable (OK): no DH EXPORT ciphers, no DH key detected with <= TLS 1.2
 BEAST (CVE-2011-3389)                     TLS1: ECDHE-RSA-AES128-SHA AES128-SHA ECDHE-RSA-AES256-SHA AES256-SHA DES-CBC3-SHA
                                           VULNERABLE -- but also supports higher protocols  TLSv1.1 TLSv1.2 (likely mitigated)
 LUCKY13 (CVE-2013-0169), experimental     potentially VULNERABLE, uses cipher block chaining (CBC) ciphers with TLS. Check patches
 Winshock (CVE-2014-6321), experimental    not vulnerable (OK)
 RC4 (CVE-2013-2566, CVE-2015-2808)        no RC4 ciphers detected (OK)


 Done 2022-10-09 20:58:34 [  39s] -->> 172.67.21.207:443 (www.netology.ru) <<--

--------------------------------------------------------------------------------------------
 Start 2022-10-09 20:58:35        -->> 104.22.41.171:443 (www.netology.ru) <<--

 Further IP addresses:   104.22.40.171 172.67.21.207 2606:4700:10::6816:29ab 2606:4700:10::ac43:15cf 2606:4700:10::6816:28ab
 rDNS (104.22.41.171):   --
 Service detected:       HTTP


 Testing vulnerabilities

 Heartbleed (CVE-2014-0160)                not vulnerable (OK), no heartbeat extension
 CCS (CVE-2014-0224)                       not vulnerable (OK)
 Ticketbleed (CVE-2016-9244), experiment.  not vulnerable (OK), no session tickets
 ROBOT                                     not vulnerable (OK)
 Secure Renegotiation (RFC 5746)           OpenSSL handshake didn't succeed
 Secure Client-Initiated Renegotiation     not vulnerable (OK)
 CRIME, TLS (CVE-2012-4929)                not vulnerable (OK)
 BREACH (CVE-2013-3587)                    no gzip/deflate/compress/br HTTP compression (OK)  - only supplied "/" tested
 POODLE, SSL (CVE-2014-3566)               not vulnerable (OK)
 TLS_FALLBACK_SCSV (RFC 7507)              Downgrade attack prevention supported (OK)
 SWEET32 (CVE-2016-2183, CVE-2016-6329)    VULNERABLE, uses 64 bit block ciphers
 FREAK (CVE-2015-0204)                     not vulnerable (OK)
 DROWN (CVE-2016-0800, CVE-2016-0703)      not vulnerable on this host and port (OK)
                                           make sure you don't use this certificate elsewhere with SSLv2 enabled services, see
                                           https://search.censys.io/search?resource=hosts&virtual_hosts=INCLUDE&q=A3C7D9A8D3805171D99EA61F5C80B8ADF49B93BA21EBB492D78512BA254E90A5
 LOGJAM (CVE-2015-4000), experimental      not vulnerable (OK): no DH EXPORT ciphers, no DH key detected with <= TLS 1.2
 BEAST (CVE-2011-3389)                     TLS1: ECDHE-RSA-AES128-SHA AES128-SHA ECDHE-RSA-AES256-SHA AES256-SHA DES-CBC3-SHA
                                           VULNERABLE -- but also supports higher protocols  TLSv1.1 TLSv1.2 (likely mitigated)
 LUCKY13 (CVE-2013-0169), experimental     potentially VULNERABLE, uses cipher block chaining (CBC) ciphers with TLS. Check patches
 Winshock (CVE-2014-6321), experimental    not vulnerable (OK)
 RC4 (CVE-2013-2566, CVE-2015-2808)        no RC4 ciphers detected (OK)


 Done 2022-10-09 20:59:12 [  77s] -->> 104.22.41.171:443 (www.netology.ru) <<--

--------------------------------------------------------------------------------------------
 Start 2022-10-09 20:59:12        -->> 104.22.40.171:443 (www.netology.ru) <<--

 Further IP addresses:   104.22.41.171 172.67.21.207 2606:4700:10::6816:29ab 2606:4700:10::ac43:15cf 2606:4700:10::6816:28ab
 rDNS (104.22.40.171):   --
 Service detected:       HTTP


 Testing vulnerabilities

 Heartbleed (CVE-2014-0160)                not vulnerable (OK), no heartbeat extension
 CCS (CVE-2014-0224)                       not vulnerable (OK)
 Ticketbleed (CVE-2016-9244), experiment.  not vulnerable (OK), no session tickets
 ROBOT                                     not vulnerable (OK)
 Secure Renegotiation (RFC 5746)           OpenSSL handshake didn't succeed
 Secure Client-Initiated Renegotiation     not vulnerable (OK)
 CRIME, TLS (CVE-2012-4929)                not vulnerable (OK)
 BREACH (CVE-2013-3587)                    no gzip/deflate/compress/br HTTP compression (OK)  - only supplied "/" tested
 POODLE, SSL (CVE-2014-3566)               not vulnerable (OK)
 TLS_FALLBACK_SCSV (RFC 7507)              Downgrade attack prevention supported (OK)
 SWEET32 (CVE-2016-2183, CVE-2016-6329)    VULNERABLE, uses 64 bit block ciphers
 FREAK (CVE-2015-0204)                     not vulnerable (OK)
 DROWN (CVE-2016-0800, CVE-2016-0703)      not vulnerable on this host and port (OK)
                                           make sure you don't use this certificate elsewhere with SSLv2 enabled services, see
                                           https://search.censys.io/search?resource=hosts&virtual_hosts=INCLUDE&q=A3C7D9A8D3805171D99EA61F5C80B8ADF49B93BA21EBB492D78512BA254E90A5
 LOGJAM (CVE-2015-4000), experimental      not vulnerable (OK): no DH EXPORT ciphers, no DH key detected with <= TLS 1.2
 BEAST (CVE-2011-3389)                     TLS1: ECDHE-RSA-AES128-SHA AES128-SHA ECDHE-RSA-AES256-SHA AES256-SHA DES-CBC3-SHA
                                           VULNERABLE -- but also supports higher protocols  TLSv1.1 TLSv1.2 (likely mitigated)
 LUCKY13 (CVE-2013-0169), experimental     potentially VULNERABLE, uses cipher block chaining (CBC) ciphers with TLS. Check patches
 Winshock (CVE-2014-6321), experimental    not vulnerable (OK)
 RC4 (CVE-2013-2566, CVE-2015-2808)        no RC4 ciphers detected (OK)


 Done 2022-10-09 20:59:50 [ 115s] -->> 104.22.40.171:443 (www.netology.ru) <<--

--------------------------------------------------------------------------------------------
Done testing now all IP addresses (on port 443): 172.67.21.207 104.22.41.171 104.22.40.171
```

5. Установите на Ubuntu ssh сервер, сгенерируйте новый приватный ключ. Скопируйте свой публичный ключ на другой сервер. Подключитесь к серверу по SSH-ключу.
 #### Решение:
```
WORKER:~/DevOps$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/minerstat/.ssh/id_rsa):
/home/minerstat/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/minerstat/.ssh/id_rsa.
Your public key has been saved in /home/minerstat/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:D5i+bgathZkG4h4hyYi1608SL+72nCYLZaubyHEMiWU minerstat@MAIN_WORKER
The key's randomart image is:
+---[RSA 2048]----+
|                 |
|  .              |
|+oE.             |
|B=+    o         |
|++=o =o S        |
| =++*.o  o       |
|oo=+o+.   .      |
|o**=o.o.         |
|==+*=+o          |
+----[SHA256]-----+
WORKER:~/DevOps$ ssh-copy-id vagrant@192.168.1.37
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/minerstat/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
vagrant@192.168.1.37's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'vagrant@192.168.1.37'"
and check to make sure that only the key(s) you wanted were added.
WORKER:~/DevOps$ ssh vagrant@192.168.1.37
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-91-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sun 09 Oct 2022 09:32:39 PM UTC

  System load:             0.0
  Usage of /:              12.2% of 30.88GB
  Memory usage:            10%
  Swap usage:              0%
  Processes:               134
  Users logged in:         1
  IPv4 address for dummy0: 10.30.0.2
  IPv4 address for eth0:   10.0.2.15
  IPv4 address for eth1:   192.168.1.37
  IPv6 address for eth1:   fd1c:d07:f478:4e5a:a00:27ff:feba:6307


This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento
Last login: Sun Oct  9 21:28:20 2022 from 192.168.1.27
```

6. Переименуйте файлы ключей из задания 5. Настройте файл конфигурации SSH клиента, так чтобы вход на удаленный сервер осуществлялся по имени сервера.
#### Решение:
```
WORKER:~/.ssh$ mkdir -p ~/.ssh && chmod 700 ~/.ssh
WORKER:~/.ssh$ touch config && chmod 600 config
WORKER:~/.ssh$ nano config
WORKER:~/.ssh$ cat config
Host vagrant_server
        HostName 192.168.1.37
        IdentityFile ~/.ssh/vagrant_server.key
        User vagrant
WORKER:~/.ssh$ cp id_rsa vagrant_server.key
WORKER:~/.ssh$ ssh vagrant_server
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-91-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sun 09 Oct 2022 09:45:26 PM UTC

  System load:             0.0
  Usage of /:              12.2% of 30.88GB
  Memory usage:            10%
  Swap usage:              0%
  Processes:               131
  Users logged in:         1
  IPv4 address for dummy0: 10.30.0.2
  IPv4 address for eth0:   10.0.2.15
  IPv4 address for eth1:   192.168.1.37
  IPv6 address for eth1:   fd1c:d07:f478:4e5a:a00:27ff:feba:6307


This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento
Last login: Sun Oct  9 21:44:21 2022 from 192.168.1.27
vagrant@vagrant:~$
```

7. Соберите дамп трафика утилитой tcpdump в формате pcap, 100 пакетов. Откройте файл pcap в Wireshark.
#### Решение:
```
vagrant@vagrant:~$ sudo tcpdump -D
1.eth0 [Up, Running]
2.dummy0 [Up, Running]
3.eth1 [Up, Running]
4.lo [Up, Running, Loopback]
5.any (Pseudo-device that captures on all interfaces) [Up, Running]
6.bluetooth-monitor (Bluetooth Linux Monitor) [none]
7.nflog (Linux netfilter log (NFLOG) interface) [none]
8.nfqueue (Linux netfilter queue (NFQUEUE) interface) [none]
vagrant@vagrant:~$ sudo tcpdump -c 100 -w dump.pcap -i 3
tcpdump: listening on eth1, link-type EN10MB (Ethernet), capture size 262144 bytes
100 packets captured
143 packets received by filter
0 packets dropped by kernel
```
![tcpdump PCAP capture](https://github.com/dmitriy-dronov-550503/sysadm-homeworks/blob/devsys10/03-sysadmin-09-security/Pcap.PNG?raw=true)  

 ---

## Задание для самостоятельной отработки (необязательно к выполнению)

8*. Просканируйте хост scanme.nmap.org. Какие сервисы запущены?

9*. Установите и настройте фаервол ufw на web-сервер из задания 3. Откройте доступ снаружи только к портам 22,80,443
