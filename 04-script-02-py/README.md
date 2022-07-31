# Домашнее задание к занятию "4.2. Использование Python для решения типовых DevOps задач"

## Обязательные задания

1. Есть скрипт:
	```python
    #!/usr/bin/env python3
	a = 1
	b = '2'
	c = a + b
	```
	* Какое значение будет присвоено переменной c?
	* Как получить для переменной c значение 12?
	* Как получить для переменной c значение 3?
	
#### Ответ:
 Значение переменной **c** не будет присвоено, так как выполняется операция с несовместимыми типами, будет сообщение об ошибке	
```py
TypeError: unsupported operand type(s) for +: 'int' and 'str'
```
Чтобы получить для переменной **c** значение 12, требуется привести переменную **a** к типу str, соответственно, c=str(a)+b
```bash
avdeevan@bhdevops:~$ cat 2.py
#!/usr/bin/env python3
a = 1
b = '2'
c = str(a) + b
print(c)
avdeevan@bhdevops:~$ ./2.py
12
avdeevan@bhdevops:~$
```	
Аналогично, чтобы получить для переменной **с** значение **3**, требуется привести переменную **b** к типу int, c=a + int(b)
```py
avdeevan@bhdevops:~$ cat 2.py
#!/usr/bin/env python3
a = 1
b = '2'
c = a + int(b)
print(c)
avdeevan@bhdevops:~$ ./2.py
3
avdeevan@bhdevops:~$
```


2. Мы устроились на работу в компанию, где раньше уже был DevOps Engineer. Он написал скрипт, позволяющий узнать, какие файлы модифицированы в репозитории, относительно локальных изменений. Этим скриптом недовольно начальство, потому что в его выводе есть не все изменённые файлы, а также непонятен полный путь к директории, где они находятся. Как можно доработать скрипт ниже, чтобы он исполнял требования вашего руководителя?

```py
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
	
## Ваш скрипт:

```py
#!/usr/bin/env python3

import os

bash_command = ["cd ~/netology/sysadm-homeworks", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(os.getcwd()+bash_command[0][4:]+'/'+prepare_result)
```

## Вывод скрипта при запуске при тестировании:
```python
avdeevan@bhdevops:~$ ./2.py
/home/avdeevan/netology/sysadm-homeworks/04-script-02-py/README.md
/home/avdeevan/netology/sysadm-homeworks/README.md
avdeevan@bhdevops:~$
```
3. Доработать скрипт выше так, чтобы он мог проверять не только локальный репозиторий в текущей директории, а также умел воспринимать путь к репозиторию, который мы передаём как входной параметр. Мы точно знаем, что начальство коварное и будет проверять работу этого скрипта в директориях, которые не являются локальными репозиториями.

## Ваш скрипт:

```py

#!/usr/bin/env python3
import os
import sys

def checkgitdir(dir):
    for i in  os.listdir(dir):
     if '.git' == i:
      return 1

if len(sys.argv) < 2 :
  print('Script running whithout arguments! Will be used local directory!')
  if checkgitdir(os.getcwd()) == 1:
    bash_command = ["git status"]
    result_os = os.popen(' && '.join(bash_command)).read()
    is_change = False
    for result in result_os.split('\n'):
      if result.find('modified') != -1:
         prepare_result = result.replace('\tmodified:   ', '')
         print(os.getcwd() +'/'+prepare_result)
  else:
   print('It\'s not git repositary directory, please re-run script with argument:  path git-dir,example:  ./2py /my/git/dir  or run FROM git dir. Exit')
   sys.exit()
elif len(sys.argv) == 2:
 if os.path.isdir(sys.argv[1]):
  if checkgitdir(sys.argv[1]) == 1:
    bash_command = ["cd "+sys.argv[1], "git status"]
    result_os = os.popen(' && '.join(bash_command)).read()
    is_change = False
    for result in result_os.split('\n'):
      if result.find('modified') != -1:
         prepare_result = result.replace('\tmodified:   ', '')
         print(os.getcwd() +'/'+prepare_result)
  else:
   print('It\'s not git repositary directory, please re-run script with valid argument:  path git-dir,example:  ./2py /my/git/dir  or run FROM git dir. Exit')
 else:
  print ('Directory: '+sys.argv[1] '+ not found. Please re-run script with corrent arguments')

```

## Вывод скрипта при запуске при тестировании:

```python

avdeevan@bhdevops:~/netology/sysadm-homeworks$ ./2.py               #запуск из каталога репа, в котором были изменения
Script running whithout arguments! Will be used local directory!
/home/avdeevan/netology/sysadm-homeworks/04-script-02-py/README.md
/home/avdeevan/netology/sysadm-homeworks/README.md
avdeevan@bhdevops:~/netology/sysadm-homeworks$
avdeevan@bhdevops:~/netology/sysadm-homeworks$ ./2.py /dev/   #указана существующая директория, не являющаяся git-репом
It's not git repositary directory, please re-run script with valid argument:  path git-dir,example:  ./2py /my/git/dir  or run FROM git dir. Exit
avdeevan@bhdevops:~/netology/sysadm-homeworks$
avdeevan@bhdevops:~/netology/sysadm-homeworks$ ./2.py /de     #указан невалидный каталог
Directory: /de not found. Please re-run script with corrent arguments
avdeevan@bhdevops:~/netology/sysadm-homeworks$
avdeevan@bhdevops:~/netology/sysadm-homeworks$ ./2.py ~/netology/sysadm-homeworks/    #целевой git-реп, в котором были изменения!
/home/avdeevan/netology/sysadm-homeworks/04-script-02-py/README.md
/home/avdeevan/netology/sysadm-homeworks/README.md
avdeevan@bhdevops:~/netology/sysadm-homeworks$
avdeevan@bhdevops:~/netology/sysadm-homeworks$ ./2.py ~/sysadm-homeworks/    #также валидный git-реп, в котором НЕТ изменений.
avdeevan@bhdevops:~/netology/sysadm-homeworks$
avdeevan@bhdevops:~/netology/sysadm-homeworks$


```
4. Наша команда разрабатывает несколько веб-сервисов, доступных по http. Мы точно знаем, что на их стенде нет никакой балансировки, кластеризации, за DNS прячется конкретный IP сервера, где установлен сервис. Проблема в том, что отдел, занимающийся нашей инфраструктурой очень часто меняет нам сервера, поэтому IP меняются примерно раз в неделю, при этом сервисы сохраняют за собой DNS имена. Это бы совсем никого не беспокоило, если бы несколько раз сервера не уезжали в такой сегмент сети нашей компании, который недоступен для разработчиков. Мы хотим написать скрипт, который опрашивает веб-сервисы, получает их IP, выводит информацию в стандартный вывод в виде: <URL сервиса> - <его IP>. Также, должна быть реализована возможность проверки текущего IP сервиса c его IP из предыдущей проверки. Если проверка будет провалена - оповестить об этом в стандартный вывод сообщением: [ERROR] <URL сервиса> IP mismatch: <старый IP> <Новый IP>. Будем считать, что наша разработка реализовала сервисы: drive.google.com, mail.google.com, google.com.

## Ваш скрипт:

```py

  GNU nano 6.2                                                                                                                                                 5.py
#!/usr/bin/env python3
import socket
import json
import os

check_dict = {}
hosts_file = 'hosts'
log_file = 'log.json'

#loading list of checked hosts from file ./hosts and load last check from file ./log.json to check_dict

if os.path.isfile(log_file):
 log = open(log_file, "r")
 check_dict = json.load(log)
else:
 with open(hosts_file) as file:
  arr_site = [row.strip() for row in file]
  for site in arr_site:
   host = socket.gethostbyname(site)
   check_dict.update({site:host})

#run check and compare with last check

for url,ip in check_dict.items():
 new_ip = socket.gethostbyname(url)
 if new_ip == ip:
  print(url + ' - ' + ip)
 else:
  check_dict[url] = new_ip
  print('[ERROR] site IP mismatch: '+url+' '+ ip +' ' + new_ip)

 log = open(log_file, "w")
 json.dump(check_dict,log)
 log.close()

```

## Вывод скрипта при запуске при тестировании:

```python
root@bhdevops:/home/avdeevan# cat log.json #вывожу значения прошлой проверки
{"drive.google.com": "64.233.161.194", "mail.google.com": "173.194.222.17", "google.com": "64.233.165.100"}root@bhdevops:/home/avdeevan#
root@bhdevops:/home/avdeevan#
root@bhdevops:/home/avdeevan#
root@bhdevops:/home/avdeevan# ./5.py        #выполняю новую проверку
[ERROR] site IP mismatch: drive.google.com 64.233.161.194 173.194.222.194
[ERROR] site IP mismatch: mail.google.com 173.194.222.17 173.194.222.83
[ERROR] site IP mismatch: google.com 64.233.165.100 64.233.164.139
root@bhdevops:/home/avdeevan#
root@bhdevops:/home/avdeevan#
root@bhdevops:/home/avdeevan#
root@bhdevops:/home/avdeevan#
root@bhdevops:/home/avdeevan# cat log.json   #демонстрация измененного файла после запуска скрипта
{"drive.google.com": "173.194.222.194", "mail.google.com": "173.194.222.83", "google.com": "64.233.164.139"}root@bhdevops:/home/avdeevan#
root@bhdevops:/home/avdeevan#
root@bhdevops:/home/avdeevan#
root@bhdevops:/home/avdeevan#
root@bhdevops:/home/avdeevan# ./5.py
drive.google.com - 173.194.222.194
mail.google.com - 173.194.222.83
[ERROR] site IP mismatch: google.com 64.233.164.139 64.233.164.102
root@bhdevops:/home/avdeevan#
root@bhdevops:/home/avdeevan#
root@bhdevops:/home/avdeevan#
root@bhdevops:/home/avdeevan#
root@bhdevops:/home/avdeevan# cat log.json
{"drive.google.com": "173.194.222.194", "mail.google.com": "173.194.222.83", "google.com": "64.233.164.102"}root@bhdevops:/home/avdeevan#
root@bhdevops:/home/avdeevan#
root@bhdevops:/home/avdeevan#
root@bhdevops:/home/avdeevan#
root@bhdevops:/home/avdeevan#
root@bhdevops:/home/avdeevan#
root@bhdevops:/home/avdeevan#
root@bhdevops:/home/avdeevan# cat hosts      #вывод тестируемых хостов с целью удобстава добавления без правки кода
drive.google.com
mail.google.com
google.com
root@bhdevops:/home/avdeevan#



```


