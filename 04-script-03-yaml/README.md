### Как сдавать задания

Вы уже изучили блок «Системы управления версиями», и начиная с этого занятия все ваши работы будут приниматься ссылками на .md-файлы, размещённые в вашем публичном репозитории.

Скопируйте в свой .md-файл содержимое этого файла; исходники можно посмотреть [здесь](https://raw.githubusercontent.com/netology-code/sysadm-homeworks/devsys10/04-script-03-yaml/README.md). Заполните недостающие части документа решением задач (заменяйте `???`, ОСТАЛЬНОЕ В ШАБЛОНЕ НЕ ТРОГАЙТЕ чтобы не сломать форматирование текста, подсветку синтаксиса и прочее, иначе можно отправиться на доработку) и отправляйте на проверку. Вместо логов можно вставить скриншоты по желани.

# Домашнее задание к занятию "4.3. Языки разметки JSON и YAML"


## Обязательная задача 1
Мы выгрузили JSON, который получили через API запрос к нашему сервису:
```
    { "info" : "Sample JSON output from our service\t",
        "elements" :[
            { "name" : "first",
            "type" : "server",
            "ip" : 7175 
            }
            { "name" : "second",
            "type" : "proxy",
            "ip : 71.78.22.43
            }
        ]
    }
```
  Нужно найти и исправить все ошибки, которые допускает наш сервис
  
   ### Корректный json-файл:
```json
{
  "info": "Sample JSON output from our service\t",
  "elements": [
    {
      "name": "first",
      "type": "server",
      "ip": "7175"
    },
    {
      "name": "second",
      "type": "proxy",
      "ip": "71.78.22.43"
    }
  ]
}
```
  

## Обязательная задача 2
В прошлый рабочий день мы создавали скрипт, позволяющий опрашивать веб-сервисы и получать их IP. К уже реализованному функционалу нам нужно добавить возможность записи JSON и YAML файлов, описывающих наши сервисы. Формат записи JSON по одному сервису: `{ "имя сервиса" : "его IP"}`. Формат записи YAML по одному сервису: `- имя сервиса: его IP`. Если в момент исполнения скрипта меняется IP у сервиса - он должен так же поменяться в yml и json файле.

### Ваш скрипт:
```python
#!/usr/bin/env python3
import socket
import json
import os
import yaml



check_dict =[]
hosts_file = 'hosts'
data_file = 'data.json'
yaml_file = 'data.yaml'

#loading list of checked hosts from file ./hosts and load last check from file ./data.json to check_dict


if os.path.isfile(data_file):
 with open(data_file, "r") as read_file:
    check_dict = json.load(read_file)
else:
 with open(hosts_file) as file:
  arr_site = [row.strip() for row in file]
  for site in arr_site:
   host = socket.gethostbyname(site)
   check_dict.append({'site': site, 'ip': host})




#run check and compare with last check

for i in range(len(check_dict)):
 new_ip = socket.gethostbyname(check_dict[i]['site'])
 if new_ip == check_dict[i]['ip']:
    print(check_dict[i]['site'] + ' - ' + check_dict[i]['ip'])
 else:
  print('[ERROR] site IP mismatch: '+check_dict[i]['site']+' '+ check_dict[i]['ip'] +' ' + new_ip)
  check_dict[i]['ip'] = new_ip
 
 log = open(data_file, "w")
 json.dump(check_dict,log, indent=4)
 log.close()

with open(yaml_file, 'w') as file:
  data = yaml.dump(check_dict, file, sort_keys=False, default_flow_style=False)

```

### Вывод скрипта при запуске при тестировании:
```
[ERROR] site IP mismatch: drive.google.com 74.125.131.194 142.251.1.194
[ERROR] site IP mismatch: mail.google.com 142.251.1.18 142.251.1.19
[ERROR] site IP mismatch: google.com 142.250.150.101 142.251.1.113
```

### json-файл(ы), который(е) записал ваш скрипт:
```json
[
    {
        "site": "drive.google.com",
        "ip": "142.251.1.194"
    },
    {
        "site": "mail.google.com",
        "ip": "142.251.1.18"
    },
    {
        "site": "google.com",
        "ip": "142.251.1.113"
    }
]
```

### yml-файл(ы), который(е) записал ваш скрипт:
```yaml
- site: drive.google.com
  ip: 142.251.1.194
- site: mail.google.com
  ip: 142.251.1.18
- site: google.com
  ip: 142.251.1.113
```
