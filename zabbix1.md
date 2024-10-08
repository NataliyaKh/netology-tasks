# Домашнее задание к занятию "Система мониторинга Zabbix". Наталия Ханова


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/git-hw или  https://github.com/имя-вашего-репозитория/7-1-ansible-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.
   
Желаем успехов в выполнении домашнего задания!

---

### Задание 1

Установите Zabbix Server с веб-интерфейсом.

`Процесс выполнения`

1.    Выполняя ДЗ, сверяйтесь с процессом отражённым в записи лекции.
2.    Установите PostgreSQL. Для установки достаточна та версия, что есть в системном репозитороии Debian 11.
3.    Пользуясь конфигуратором команд с официального сайта, составьте набор команд для установки последней версии Zabbix с поддержкой PostgreSQL и Apache.
4.    Выполните все необходимые команды для установки Zabbix Server и Zabbix Web Server.

`Требования к результатам`

1.    Прикрепите в файл README.md скриншот авторизации в админке.
2.    Приложите в файл README.md текст использованных команд в GitHub.

![Страница авторизации](https://data0.gallery.ru/albums/gallery/435409-26abe-132644082-m750x740-u6a1f8.jpg)`

![Авторизация состоялась](https://data0.gallery.ru/albums/gallery/435409-e3a4a-132644086--u4fcf3.jpg)`


```
sudo -s 
wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_7.0-2+ubuntu22.04_all.deb 
dpkg -i zabbix-release_7.0-2+ubuntu22.04_all.deb 
apt update 
apt install zabbix-server-pgsql zabbix-frontend-php php8.1-pgsql zabbix-apache-conf zabbix-sql-scripts 
sudo -u postgres createuser --pwprompt zabbix
#ввод пароля
#ввод пароля
sudo -u postgres createdb -O zabbix zabbix
zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix 
nano /etc/zabbix/zabbix_server.conf
#ввод пароля в файле конфигураций (поле DBPassword=...), Ctrl+O
systemctl restart zabbix-server apache2
systemctl enable zabbix-server apache2
```

---

### Задание 2

Установите Zabbix Agent на два хоста.

`Процесс выполнения`

1.    Выполняя ДЗ, сверяйтесь с процессом отражённым в записи лекции.
2.    Установите Zabbix Agent на 2 вирт.машины, одной из них может быть ваш Zabbix Server.
3.    Добавьте Zabbix Server в список разрешенных серверов ваших Zabbix Agentов.
4.    Добавьте Zabbix Agentов в раздел Configuration > Hosts вашего Zabbix Servera.
5.    Проверьте, что в разделе Latest Data начали появляться данные с добавленных агентов.

`Требования к результатам`

1.    Приложите в файл README.md скриншот раздела Configuration > Hosts, где видно, что агенты подключены к серверу
2.    Приложите в файл README.md скриншот лога zabbix agent, где видно, что он работает с сервером
3.    Приложите в файл README.md скриншот раздела Monitoring > Latest data для обоих хостов, где видны поступающие от агентов данные.
4.    Приложите в файл README.md текст использованных команд в GitHub

![Hosts](https://data0.gallery.ru/albums/gallery/435409-167a1-132646288--ub06d9.jpg)`

![zabbix_agentd.log](https://data0.gallery.ru/albums/gallery/435409-bef49-132646291-m750x740-uef994.jpg)`

![Latest data](https://data0.gallery.ru/albums/gallery/435409-3ecbd-132646290-m750x740-u2822d.jpg)`


```
sudo -s
wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_7.0-2+ubuntu22.04_all.deb
dpkg -i zabbix-release_7.0-2+ubuntu22.04_all.deb
apt update
apt install zabbix-agent
systemctl restart zabbix-agent
systemctl enable zabbix-agent
nano /etc/zabbix/zabbix-agentd.conf
#добавление нужного сервера в файл конфигураций (Server=127.0.0.1,192.168.2.0/24), Ctrl+O. На машине, где не был установлен сервер, перед сохранением потребовалось также раскомментировать LogType=file. 
systemctl restart zabbix-agent
tail /var/log/zabbix/zabbix_agentd.log
```

---

### Задание 3 со звёздочкой*

Установите Zabbix Agent на Windows (компьютер) и подключите его к серверу Zabbix.

`Требования к результату`

1. Приложите в файл README.md скриншот раздела Latest Data, где видно свободное место на диске C:


![Windows](https://data0.gallery.ru/albums/gallery/435409-62848-132646847--u3a495.jpg)`


![Windows - Latest Data - Storage](https://data0.gallery.ru/albums/gallery/435409-400e8-132646848--u62a1b.jpg)`



