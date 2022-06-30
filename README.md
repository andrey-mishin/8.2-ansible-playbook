# Домашнее задание к занятию "08.02 Работа с Playbook"

### Подготовка инфраструктуры  
В качестве инфраструктуры выбрал виртуальную машину centos7, поднятую с помощью vagrant.  
Файл Vagrantfile в репозитории.  
На виртуальной машине создал своего пользователя и ключи ssh для удобного захода по ssh.  

### Описание playbook  
Playbook стоит из двух play.  
Первый play скачивает, устанавливает и запускает службу clickhouse.  
Второй play скачивает, устанавливает и запускает службу vector.  
Используемые параметры playbook:
  - name: 
```
имя play и/или tasks
```
  - hosts: 
```
задание хоста из inventory, на котором необходимо выполнить действия описанные в tasks
```
  - handlers:
```
действие, которое нужно выполнить, если оно было вызвано с помощью параметра notify в какой-либо из 
tasks и если task завершилась со статусом changed
```
  - become: true 
```
указывает, что play и/или task нужно выполнить от root (в нашем случае только tasks)
```
  - ansible.builtin.xxx:
```
модуль ansible, который выполняет опеределённое действие в task
```
  - with_item:
```
цикл перебирающий заданные переменные описанные в ./group_vars/<файл с переменными>  
```
  - register: create_db
```
объявление переменной create_db
```
  - failed_when:
```
параметр описывающий когда выполнение task должно считаться проваленым
```
  - changed_when:
```
параметр описывающий когда выполнение task должно закончится со статусом changed
```

В исходной playbook не было tags. Дописал tags "vdownload" и "vinstall" для tasks скачивания и установки vector.  
Для проверки работы tags удалил vector и запутил playbook с --tags vinstall. Vector установился и запустился.  

### Ход работы
Кроме дописывания своего play для установки и запуска vector , аналогично лекции, дописал task принудительного запуска
handlers, параметр "ignore_errors: true" для корректного выполнения playbook с флагом --check.  
По ходу выполнения ДЗ дописал в ./group_vars/clickhouse/vars.yml переменную с версией vector.  
Между tasks "Flush handlers" и "Create database" добавил task с модулем ansible.builtin.pause: со значение 3 сек,  
т.к. запуск службы clickhouse на виртуальной машине проиходило чуть позже, чем начинала выполняться task создания  
базы данных logs.  
Для удобства запускал playbook с флагом --ask-become-pass.
