### Как сдавать задания

Вы уже изучили блок «Системы управления версиями», и начиная с этого занятия все ваши работы будут приниматься ссылками на .md-файлы, размещённые в вашем публичном репозитории.

Скопируйте в свой .md-файл содержимое этого файла; исходники можно посмотреть [здесь](https://raw.githubusercontent.com/netology-code/sysadm-homeworks/devsys10/04-script-02-py/README.md). Заполните недостающие части документа решением задач (заменяйте `???`, ОСТАЛЬНОЕ В ШАБЛОНЕ НЕ ТРОГАЙТЕ чтобы не сломать форматирование текста, подсветку синтаксиса и прочее, иначе можно отправиться на доработку) и отправляйте на проверку. Вместо логов можно вставить скриншоты по желани.

# Домашнее задание к занятию "4.2. Использование Python для решения типовых DevOps задач"

## Обязательная задача 1

Есть скрипт:
```python
#!/usr/bin/env python3
a = 1
b = '2'
c = a + b
```

### Вопросы:
| Вопрос  | Ответ                                                                        |
| ------------- |------------------------------------------------------------------------------|
| Какое значение будет присвоено переменной `c`?  | Значение не может быть присвоено, поскольку нельзя складывать число и строку |
| Как получить для переменной `c` значение 12?  | c = str(a) + b                                                                          |
| Как получить для переменной `c` значение 3?  | c = a + int(b)                                                                          |

## Обязательная задача 2
Мы устроились на работу в компанию, где раньше уже был DevOps Engineer. Он написал скрипт, позволяющий узнать, какие файлы модифицированы в репозитории, относительно локальных изменений. Этим скриптом недовольно начальство, потому что в его выводе есть не все изменённые файлы, а также непонятен полный путь к директории, где они находятся. Как можно доработать скрипт ниже, чтобы он исполнял требования вашего руководителя?

```python
#!/usr/bin/env python3

import os

bash_command = ["cd ~/netology/sysadm-homeworks", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(prepare_result)
        break
```

### Ваш скрипт:
```python
#!/usr/bin/env python3

import os

repo_path = '~/netology/sysadm-homeworks'
bash_command = ['cd {}'.format(repo_path), "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print('{}/{}'.format(os.path.abspath(repo_path), prepare_result))
```

### Вывод скрипта при запуске при тестировании:
```
$ python3 pp.py
~/netology/sysadm-homeworks/01-intro-01/netology.md
~/netology/sysadm-homeworks/03-sysadmin-04-os/README.md
~/netology/sysadm-homeworks/04-script-01-bash/README.md
~/netology/sysadm-homeworks/README.md
```

## Обязательная задача 3
1. Доработать скрипт выше так, чтобы он мог проверять не только локальный репозиторий в текущей директории, а также умел воспринимать путь к репозиторию, который мы передаём как входной параметр. Мы точно знаем, что начальство коварное и будет проверять работу этого скрипта в директориях, которые не являются локальными репозиториями.

### Ваш скрипт:
```python
#!/usr/bin/env python3

import os
import sys

repo_path = '.'
if (len(sys.argv) > 1):
    repo_path = sys.argv[1]

if not os.path.isdir(repo_path):
    print('There directory', os.path.abspath(repo_path), 'does not exist')
    exit()
if not os.path.isdir('{}/.git'.format(repo_path)):
    print('The directory', os.path.abspath(repo_path), 'is not a git repository')
    exit()

bash_command = ['cd {}'.format(repo_path), "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print('{}/{}'.format(os.path.abspath(repo_path), prepare_result))
```

### Вывод скрипта при запуске при тестировании:
```
vagrant@vagrant:~$ python3 check_mod_repo.py
The directory /home/vagrant is not a git repository

vagrant@vagrant:~$ python3 check_mod_repo.py ~/netology/sysadm-homeworks/
/home/vagrant/netology/sysadm-homeworks/01-intro-01/netology.md
/home/vagrant/netology/sysadm-homeworks/03-sysadmin-04-os/README.md
/home/vagrant/netology/sysadm-homeworks/04-script-01-bash/README.md
/home/vagrant/netology/sysadm-homeworks/README.md

vagrant@vagrant:~$ python3 check_mod_repo.py netology
The directory /home/vagrant/netology is not a git repository

vagrant@vagrant:~$ python3 check_mod_repo.py netology/some_other_repo
There directory /home/vagrant/netology/some_other_repo does not exist

vagrant@vagrant:~$ python3 check_mod_repo.py netology/other_repo/
/home/vagrant/netology/other_repo/01-intro-01/netology.md
/home/vagrant/netology/other_repo/03-sysadmin-04-os/README.md
/home/vagrant/netology/other_repo/03-sysadmin-08-net/Vagrantfile
/home/vagrant/netology/other_repo/04-script-01-bash/README.md
/home/vagrant/netology/other_repo/README.md
```

## Обязательная задача 4
1. Наша команда разрабатывает несколько веб-сервисов, доступных по http. Мы точно знаем, что на их стенде нет никакой балансировки, кластеризации, за DNS прячется конкретный IP сервера, где установлен сервис. Проблема в том, что отдел, занимающийся нашей инфраструктурой очень часто меняет нам сервера, поэтому IP меняются примерно раз в неделю, при этом сервисы сохраняют за собой DNS имена. Это бы совсем никого не беспокоило, если бы несколько раз сервера не уезжали в такой сегмент сети нашей компании, который недоступен для разработчиков. Мы хотим написать скрипт, который опрашивает веб-сервисы, получает их IP, выводит информацию в стандартный вывод в виде: <URL сервиса> - <его IP>. Также, должна быть реализована возможность проверки текущего IP сервиса c его IP из предыдущей проверки. Если проверка будет провалена - оповестить об этом в стандартный вывод сообщением: [ERROR] <URL сервиса> IP mismatch: <старый IP> <Новый IP>. Будем считать, что наша разработка реализовала сервисы: `drive.google.com`, `mail.google.com`, `google.com`.

### Ваш скрипт:
```python
#!/usr/bin/env python3

import os

DB_NAME = 'domains_and_ips.db'

domains = {'drive.google.com':'0.0.0.0',
           'mail.google.com':'0.0.0.0',
           'google.com':'0.0.0.0'}

if os.path.isfile(DB_NAME):
    try:
        with open(DB_NAME, 'r') as f:
            domains = eval(f.read())
    except:
        print('Cannot read database')
        pass

for d in domains.keys():
    bash_command = ['dig +short {} | tail -n1'.format(d)]
    ip = os.popen(' && '.join(bash_command)).read().replace('\n','')
    print(d,'-',ip)
    if domains[d] != ip:
        print('[ERROR] {} IP mismatch: {} {}'.format(d, domains[d], ip))
        domains[d] = ip

with open(DB_NAME, 'w') as f:
    f.write(str(domains))
```

### Вывод скрипта при запуске при тестировании:
```
vagrant@vagrant:~$ sudo systemd-resolve --flush-caches

vagrant@vagrant:~$ rm domains_and_ips.db

vagrant@vagrant:~$ python3 ip.py
drive.google.com - 108.177.127.194
[ERROR] drive.google.com IP mismatch: 0.0.0.0 108.177.127.194
mail.google.com - 142.250.102.19
[ERROR] mail.google.com IP mismatch: 0.0.0.0 142.250.102.19
google.com - 142.250.27.139
[ERROR] google.com IP mismatch: 0.0.0.0 142.250.27.139

vagrant@vagrant:~$ python3 ip.py
drive.google.com - 108.177.127.194
mail.google.com - 142.250.102.83
[ERROR] mail.google.com IP mismatch: 142.250.102.19 142.250.102.83
google.com - 142.250.27.138
[ERROR] google.com IP mismatch: 142.250.27.139 142.250.27.138

vagrant@vagrant:~$ python3 ip.py
drive.google.com - 108.177.127.194
mail.google.com - 142.250.102.83
google.com - 142.250.27.138

vagrant@vagrant:~$ python3 ip.py
drive.google.com - 108.177.127.194
mail.google.com - 142.250.102.83
google.com - 142.250.27.138

vagrant@vagrant:~$ python3 ip.py
drive.google.com - 108.177.127.194
mail.google.com - 142.250.102.83
google.com - 142.250.27.138
```

## Дополнительное задание (со звездочкой*) - необязательно к выполнению

Так получилось, что мы очень часто вносим правки в конфигурацию своей системы прямо на сервере. Но так как вся наша команда разработки держит файлы конфигурации в github и пользуется gitflow, то нам приходится каждый раз переносить архив с нашими изменениями с сервера на наш локальный компьютер, формировать новую ветку, коммитить в неё изменения, создавать pull request (PR) и только после выполнения Merge мы наконец можем официально подтвердить, что новая конфигурация применена. Мы хотим максимально автоматизировать всю цепочку действий. Для этого нам нужно написать скрипт, который будет в директории с локальным репозиторием обращаться по API к github, создавать PR для вливания текущей выбранной ветки в master с сообщением, которое мы вписываем в первый параметр при обращении к py-файлу (сообщение не может быть пустым). При желании, можно добавить к указанному функционалу создание новой ветки, commit и push в неё изменений конфигурации. С директорией локального репозитория можно делать всё, что угодно. Также, принимаем во внимание, что Merge Conflict у нас отсутствуют и их точно не будет при push, как в свою ветку, так и при слиянии в master. Важно получить конечный результат с созданным PR, в котором применяются наши изменения. 

### Ваш скрипт:
```python
???
```

### Вывод скрипта при запуске при тестировании:
```
???
```
