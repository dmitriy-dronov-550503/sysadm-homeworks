# Домашнее задание к занятию "3.7. Компьютерные сети, лекция 2"

1. Проверьте список доступных сетевых интерфейсов на вашем компьютере. Какие команды есть для этого в Linux и в Windows?
#### Решение:
```
C:\Users\dmitriyd>ipconfig

Windows IP Configuration


Unknown adapter Подключение по локальной сети 2:

   Connection-specific DNS Suffix  . :
   Link-local IPv6 Address . . . . . : fe80::1522:452d:2db8:271d%6
   IPv4 Address. . . . . . . . . . . : 10.9.0.6
   Subnet Mask . . . . . . . . . . . : 255.255.255.252
   Default Gateway . . . . . . . . . :

Unknown adapter ProtonVPN TUN:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . :

Ethernet adapter Ethernet:

   Connection-specific DNS Suffix  . :
   IPv4 Address. . . . . . . . . . . : 192.168.1.21
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 192.168.1.1

Ethernet adapter VirtualBox Host-Only Network:

   Connection-specific DNS Suffix  . :
   Link-local IPv6 Address . . . . . : fe80::85a7:cfd9:c9f8:bd5f%12
   IPv4 Address. . . . . . . . . . . : 192.168.56.1
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . :

Unknown adapter Подключение по локальной сети:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . :

Ethernet adapter VMware Network Adapter VMnet1:

   Connection-specific DNS Suffix  . :
   IPv4 Address. . . . . . . . . . . : 192.168.240.1
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . :

Ethernet adapter VMware Network Adapter VMnet8:

   Connection-specific DNS Suffix  . :
   IPv4 Address. . . . . . . . . . . : 192.168.179.1
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . :
   ```

```
$ ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:b1:28:5d brd ff:ff:ff:ff:ff:ff
```

2. Какой протокол используется для распознавания соседа по сетевому интерфейсу? Какой пакет и команды есть в Linux для этого?
#### Решение:
Для этого используется протокол LLDP (Link Layer Discovery Protocol). Изначально подобный протокол был создан в компании Cisco и по сей день остаётся проприетарным. Поэтому и был создан открытый протокол LLDP.
Для того, чтобы использовать этот протокол, в Linux есть пакет `lldpd`, работающий в качестве демона. После его установки, с помощью команды `lldpctl` можно увидеть соседние устройства, поддерживающие и отвечающие по протоколу LLDP.

3. Какая технология используется для разделения L2 коммутатора на несколько виртуальных сетей? Какой пакет и команды есть в Linux для этого? Приведите пример конфига.
#### Решение:
Для разделения трафика в рамках одного коммутатора на несколько виртуальных сетей существует технология VLAN. Для использования её в Linux есть одноимённый пакет `vlan`.  
Пример конфигурации VLAN 10 (10.10.0.0/24) и VLAN 20 (10.20.0.0/24):
```
vagrant@vagrant:~$ sudo apt install vlan
vagrant@vagrant:~$ sudo modprobe 8021q
vagrant@vagrant:~$ sudo ip link add link eth0 name eth0.10 type vlan id 10
vagrant@vagrant:~$ sudo ip addr add 10.10.0.1/24 dev eth0.10
vagrant@vagrant:~$ sudo ip link set up eth0.10
vagrant@vagrant:~$ sudo ip link add link eth0 name eth0.20 type vlan id 20
vagrant@vagrant:~$ sudo ip addr add 10.20.0.1/24 dev eth0.20
vagrant@vagrant:~$ sudo ip link set up eth0.20
vagrant@vagrant:~$ ip address
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:b1:28:5d brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic eth0
       valid_lft 55139sec preferred_lft 55139sec
    inet6 fe80::a00:27ff:feb1:285d/64 scope link
       valid_lft forever preferred_lft forever
3: eth0.10@eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether 08:00:27:b1:28:5d brd ff:ff:ff:ff:ff:ff
    inet 10.10.0.1/24 scope global eth0.10
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:feb1:285d/64 scope link
       valid_lft forever preferred_lft forever
4: eth0.20@eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether 08:00:27:b1:28:5d brd ff:ff:ff:ff:ff:ff
    inet 10.20.0.1/24 scope global eth0.20
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:feb1:285d/64 scope link
       valid_lft forever preferred_lft forever
```

4. Какие типы агрегации интерфейсов есть в Linux? Какие опции есть для балансировки нагрузки? Приведите пример конфига.
#### Решение:
В Linux можно выделить два типа агрегации интерфейсов: балансировка нагрузки и отказоустойчивость. Однако, есть несколько вариантов для балансировки нагрузки в зависимости от настроек, например:
* Балансировка по алгоритму round-robin. В этом случае пакеты будут поочерёдно рассылаться через все интерфейсы один за другим.
* Рассылка в зависимости от выбранного хэша. Например, XOR MAC адресов.
* 802.3ad - балансировка по данным стандартам
* Адаптивная балансировка передачи - производится в зависимости от текущей нагрузки интерфейсов, но с расчётом только исходящего трафика.
* Адаптивная балансировка - производится в зависимости от текущей нагрузки интерфейсов, как исходящего, так и входящего трафика.
Пример конфига:
```
$ sudo apt install ifenslave
$ sudo modprobe bonding
$ sudo nano /etc/network/interfaces
...
auto bond0

iface bond0 inet static
    address 10.10.0.15
    netmask 255.255.255.0
    network 10.10.0.0
    gateway 10.10.0.1
    bond-slaves eth0 eth1
    bond-mode balance-alb
    bond-miimon 100
    bond-downdelay 200
    bond-updelay 200
...
```

5. Сколько IP адресов в сети с маской /29 ? Сколько /29 подсетей можно получить из сети с маской /24. Приведите несколько примеров /29 подсетей внутри сети 10.10.10.0/24.
#### Решение:
* В сети с маской /29 можно выделить 6 IP адресов. 
* Из сети с маской /24 можно получить 32 подсети с маской /29.
* Из сети 10.10.10.0/24 можно выделить например 10.10.10.0/29, 10.10.10.8/29, 10.10.10.16/29 ... 10.10.10.240/29, 10.10.10.248/29

6. Задача: вас попросили организовать стык между 2-мя организациями. Диапазоны 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 уже заняты. Из какой подсети допустимо взять частные IP адреса? Маску выберите из расчета максимум 40-50 хостов внутри подсети.
#### Решение:
Можно выделить IP адреса из подсети 100.64.0.0/10. Например, 100.64.0.0/24.

7. Как проверить ARP таблицу в Linux, Windows? Как очистить ARP кеш полностью? Как из ARP таблицы удалить только один нужный IP?
#### Решение:
* И в Linux, и в Windows таблицу ARP можно проверить с помощью команды `arp -a`.
* Чтобы очистить ARP кеш можно использовать такую команду `sudo ip -s -s neigh flush all`
* Удалить только один IP адрес можно с помощью флага `-d` `$ arp -d 192.168.1.27`


 ---
## Задание для самостоятельной отработки (необязательно к выполнению)

 8*. Установите эмулятор EVE-ng.
 
 Инструкция по установке - https://github.com/svmyasnikov/eve-ng

 Выполните задания на lldp, vlan, bonding в эмуляторе EVE-ng.