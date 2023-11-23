"'Домашнее задание к занятию 2 «Кластеризация и балансировка нагрузки»'" - `Копаческу Владимир`

Цель задания
В результате выполнения этого задания вы научитесь:

Настраивать балансировку с помощью HAProxy
Настраивать связку HAProxy + Nginx
Чеклист готовности к домашнему заданию
Установлена операционная система Ubuntu на виртуальную машину и имеется доступ к терминалу
Просмотрены конфигурационные файлы, рассматриваемые на лекции, которые находятся по ссылке
Инструкция по выполнению домашнего задания
Сделайте fork репозитория c шаблоном решения к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/gitlab-hw или https://github.com/имя-вашего-репозитория/8-03-hw).
Выполните клонирование этого репозитория к себе на ПК с помощью команды git clone.
Выполните домашнее задание и заполните у себя локально этот файл README.md:
впишите вверху название занятия и ваши фамилию и имя;
в каждом задании добавьте решение в требуемом виде: текст/код/скриншоты/ссылка;
для корректного добавления скриншотов воспользуйтесь инструкцией «Как вставить скриншот в шаблон с решением»;
при оформлении используйте возможности языка разметки md. Коротко об этом можно посмотреть в инструкции по MarkDown.
После завершения работы над домашним заданием сделайте коммит (git commit -m "comment") и отправьте его на Github (git push origin).
Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
Любые вопросы задавайте в чате учебной группы и/или в разделе «Вопросы по заданию» в личном кабинете.


### Задание 1
Запустите два simple python сервера на своей виртуальной машине на разных портах
Установите и настройте HAProxy, воспользуйтесь материалами к лекции по ссылке
Настройте балансировку Round-robin на 4 уровне.
На проверку направьте конфигурационный файл haproxy, скриншоты, где видно перенаправление запросов на разные серверы при обращении к HAProxy.

### Решение 1

`Запуск серверов`                                    
![alt text](https://github.com/Replica63/Keepalived/blob/main/img/1.png)
![alt text](https://github.com/Replica63/Keepalived/blob/main/img/2.png)
![alt text](https://github.com/Replica63/Keepalived/blob/main/img/3.png)
![alt text](https://github.com/Replica63/Keepalived/blob/main/img/4.png)
![Схема](https://github.com/Replica63/Keepalived/blob/main/img/hsrp_advanced-kopacheskuvv.pkt)

---

### Задание 2
Запустите три simple python сервера на своей виртуальной машине на разных портах
Настройте балансировку Weighted Round Robin на 7 уровне, чтобы первый сервер имел вес 2, второй - 3, а третий - 4
HAproxy должен балансировать только тот http-трафик, который адресован домену example.local
На проверку направьте конфигурационный файл haproxy, скриншоты, где видно перенаправление запросов на разные серверы при обращении к HAProxy c использованием домена example.local и без него.
### Решение 2

`Скрин двух ВМ:`
![alt text](https://github.com/Replica63/Keepalived/blob/main/img/2.1.png)
![alt text](https://github.com/Replica63/Keepalived/blob/main/img/2.2.png)

`Конфигурация первого сервера "MASTER"`

```
 global_defs {
    enable_script_security
}

vrrp_script check_script {
      script "/home/kvv/keepalived/script.sh"
      interval 3
}

vrrp_instance www {
        state MASTER
        interface enp0s8
        virtual_router_id 4
        priority 255
        advert_int 1

        virtual_ipaddress {
             192.168.1.250/24
        }
}

```
`Конфигурация второго сервера "BACKUP"`

```
 global_defs {
    enable_script_security
}

vrrp_script check_script {
      script "/home/kvv/keepalived/script.sh"
      interval 3
}

vrrp_instance www {
        state BACKUP
        interface enp0s8
        virtual_router_id 4
        priority 222
        advert_int 1

        virtual_ipaddress {
             192.168.1.250/24
        }
}

```

`Скрипт файла script.sh`

```
#!/bin/bash
if [[ $(netstat -tuln | grep LISTEN | grep :80) ]] && [[ -f /var/www/html/index.html ]]; then
        exit 0
else
        sudo systemctl stop keepalived
fi
```

`Демонстрация keepalived:`

![alt text](https://github.com/Replica63/Keepalived/blob/main/img/2.3.png)
![alt text](https://github.com/Replica63/Keepalived/blob/main/img/2.4.png)
![alt text](https://github.com/Replica63/Keepalived/blob/main/img/2.5.png)
![alt text](https://github.com/Replica63/Keepalived/blob/main/img/2.6.png)
