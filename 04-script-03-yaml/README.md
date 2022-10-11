### Как сдавать задания

Вы уже изучили блок «Системы управления версиями», и начиная с этого занятия все ваши работы будут приниматься ссылками на .md-файлы, размещённые в вашем публичном репозитории.

Скопируйте в свой .md-файл содержимое этого файла; исходники можно посмотреть [здесь](https://raw.githubusercontent.com/netology-code/sysadm-homeworks/devsys10/04-script-03-yaml/README.md). Заполните недостающие части документа решением задач (заменяйте `???`, ОСТАЛЬНОЕ В ШАБЛОНЕ НЕ ТРОГАЙТЕ чтобы не сломать форматирование текста, подсветку синтаксиса и прочее, иначе можно отправиться на доработку) и отправляйте на проверку. Вместо логов можно вставить скриншоты по желани.

# Домашнее задание к занятию "4.3. Языки разметки JSON и YAML"


## Обязательная задача 1
Мы выгрузили JSON, который получили через API запрос к нашему сервису:
```
    {
        "info" : "Sample JSON output from our service\t",
        "elements" :
        [
            {
                "name" : "first",
                "type" : "server",
                "ip" : 7175 
            },
            {
                "name" : "second",
                "type" : "proxy",
                "ip" : "71.78.22.43"
            }
        ]
    }
```
  Нужно найти и исправить все ошибки, которые допускает наш сервис

## Обязательная задача 2
В прошлый рабочий день мы создавали скрипт, позволяющий опрашивать веб-сервисы и получать их IP. К уже реализованному функционалу нам нужно добавить возможность записи JSON и YAML файлов, описывающих наши сервисы. Формат записи JSON по одному сервису: `{ "имя сервиса" : "его IP"}`. Формат записи YAML по одному сервису: `- имя сервиса: его IP`. Если в момент исполнения скрипта меняется IP у сервиса - он должен так же поменяться в yml и json файле.

### Ваш скрипт:
```python
#!/usr/bin/env python3

import os
import json
import yaml

DB_JSON_NAME = 'domains_and_ips.json'
DB_YAML_NAME = 'domains_and_ips.yaml'

domains = {'drive.google.com':'0.0.0.0',
           'mail.google.com':'0.0.0.0',
           'google.com':'0.0.0.0'}

def check_DB(name):
    data = None
    if os.path.isfile(name):
        try:
            with open(name, 'r') as f:
                data = f.read()
        except:
            print('Cannot read database', name)
            pass
    return data

json_data = check_DB(DB_JSON_NAME)
yaml_data = check_DB(DB_YAML_NAME)

if json_data is not None:
    domains = json.loads(json_data)
elif yaml_data is not None:
    domains = yaml.load(yaml_data)

for d in domains.keys():
    bash_command = ['dig +short {} | tail -n1'.format(d)]
    ip = os.popen(' && '.join(bash_command)).read().replace('\n','')
    print(d,'-',ip)
    if domains[d] != ip:
        print('[ERROR] {} IP mismatch: {} {}'.format(d, domains[d], ip))
        domains[d] = ip


with open(DB_JSON_NAME, 'w') as f:
    f.write(json.dumps(domains))

with open(DB_YAML_NAME, 'w') as f:
    f.write(yaml.dump(domains))
```

### Вывод скрипта при запуске при тестировании:
```
vagrant@vagrant:~/test_dir$ ls
ip2.py

vagrant@vagrant:~/test_dir$ python3 ip2.py
drive.google.com - 142.250.102.194
[ERROR] drive.google.com IP mismatch: 0.0.0.0 142.250.102.194
mail.google.com - 142.250.102.17
[ERROR] mail.google.com IP mismatch: 0.0.0.0 142.250.102.17
google.com - 142.250.102.100
[ERROR] google.com IP mismatch: 0.0.0.0 142.250.102.100

vagrant@vagrant:~/test_dir$ python3 ip2.py
drive.google.com - 142.250.102.194
mail.google.com - 142.250.102.17
google.com - 142.250.102.100

vagrant@vagrant:~/test_dir$ python3 ip2.py
drive.google.com - 142.250.102.194
mail.google.com - 142.250.102.17
google.com - 142.250.102.100

vagrant@vagrant:~/test_dir$ python3 ip2.py
drive.google.com - 142.250.102.194
mail.google.com - 142.250.102.17
google.com - 142.250.102.100

vagrant@vagrant:~/test_dir$ sudo systemd-resolve --flush-caches

vagrant@vagrant:~/test_dir$ python3 ip2.py
drive.google.com - 108.177.126.194
[ERROR] drive.google.com IP mismatch: 142.250.102.194 108.177.126.194
mail.google.com - 142.250.102.83
[ERROR] mail.google.com IP mismatch: 142.250.102.17 142.250.102.83
google.com - 142.250.27.101
[ERROR] google.com IP mismatch: 142.250.102.100 142.250.27.101

vagrant@vagrant:~/test_dir$ python3 ip2.py
drive.google.com - 108.177.126.194
mail.google.com - 142.250.102.19
[ERROR] mail.google.com IP mismatch: 142.250.102.83 142.250.102.19
google.com - 142.250.27.139
[ERROR] google.com IP mismatch: 142.250.27.101 142.250.27.139

vagrant@vagrant:~/test_dir$ python3 ip2.py
drive.google.com - 108.177.126.194
mail.google.com - 142.250.102.19
google.com - 142.250.27.139

vagrant@vagrant:~/test_dir$ python3 ip2.py
drive.google.com - 108.177.126.194
mail.google.com - 142.250.102.19
google.com - 142.250.27.139

vagrant@vagrant:~/test_dir$ python3 ip2.py
drive.google.com - 108.177.126.194
mail.google.com - 142.250.102.19
google.com - 142.250.27.139
```

### json-файл(ы), который(е) записал ваш скрипт:
```json
{"drive.google.com": "108.177.126.194", "mail.google.com": "142.250.102.19", "google.com": "142.250.27.139"}
```

### yml-файл(ы), который(е) записал ваш скрипт:
```yaml
drive.google.com: 108.177.126.194
google.com: 142.250.27.139
mail.google.com: 142.250.102.19
```

## Дополнительное задание (со звездочкой*) - необязательно к выполнению

Так как команды в нашей компании никак не могут прийти к единому мнению о том, какой формат разметки данных использовать: JSON или YAML, нам нужно реализовать парсер из одного формата в другой. Он должен уметь:
   * Принимать на вход имя файла
   * Проверять формат исходного файла. Если файл не json или yml - скрипт должен остановить свою работу
   * Распознавать какой формат данных в файле. Считается, что файлы *.json и *.yml могут быть перепутаны
   * Перекодировать данные из исходного формата во второй доступный (из JSON в YAML, из YAML в JSON)
   * При обнаружении ошибки в исходном файле - указать в стандартном выводе строку с ошибкой синтаксиса и её номер
   * Полученный файл должен иметь имя исходного файла, разница в наименовании обеспечивается разницей расширения файлов

### Ваш скрипт:
```python
???
```

### Пример работы скрипта:
???
