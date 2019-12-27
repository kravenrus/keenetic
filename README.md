## Устанавливаем Debian 10
* #### Дистрибутив взять можно [**здесь**](http://ndm.zyxmon.org/binaries/debian/)
  * Форматируем накопитель в **EXT4** и подключаем к роутеру
  * Через веб-интерфейс роутера **даем общий доступ** к подключенному накопителю и **подключаемся** к нему
  * Создаем папку **install** и помещаем в нее дистрибутив формата **.tar.gz**
* #### Входим в SSH Debian:
  * Адрес по умолчанию: **192.168.1.1:222**
  * Логин: **root**
  * Пароль: **debian**
    * Меняем пароль: `passwd`
    * Обновляем пакеты: `apt update`
    * Устанавливаем набор сетевых инструментов: `apt install net-tools`
## Установка медиа сервера - Minidlna
`apt install minidlna`
* #### Настраиваем Minidlna:
  * Основные параметры **/etc/minidlna.conf**:
    ```
    media_dir=/var/lib/minidlna
    merge_media_dirs=yes
    root_container=B
    friendly_name=Name
    ```
    [Подробная информация по настройке конфига](http://itadept.ru/linux-dlna-server-minidlna/ "Подробная информация по настройке конфига")
* #### Запускаем Minidlna:
  `/etc/init.d/minidlna start`
  * ###### Для запуска службы вместе с Debian добавляем строку в конец файла chroot-services.list
    ```
    minidlna
    ```
## Установка Bittorrent клиента - Transmission
`apt install transmission-daemon`
* #### Настраиваем Transmission:
  * Меняем пользователя в **/etc/init.d/transmission-daemon**:
    ```
    USER=root
    ```
    ###### Либо создаем пользователя, даем права и меняем на значение его имени
  * Основные параметры **/etc/transmission-daemon/settings.json**:
    ```
    "bind-address-ipv4": "192.168.1.1"
    "rpc-password": "password"
    "rpc-username": "username"
    "rpc-port": 9091,
    "rpc-whitelist-enabled": false,
    ```
    ###### Также настраивается через веб-интерфейс и Transmission Qt Client после первой инициализации
    [Подробная информация по настройке конфига](https://pcminipro.ru/os/nastrojka-transmission-daemon-settings-json/ "Подробная информация по настройке конфига")
* #### Запускаем Transmission:
  `/etc/init.d/transmission-daemon start`
  * ###### Для запуска службы вместе с Debian добавляем строку в конец файла chroot-services.list
    ```
    transmission-daemon
    ```
* **НЕ ЗАБЫВАЕМ ПРОБРОСИТЬ ПОРТ ИЗ ПАРАМЕТРА "peer-port" ДЛЯ ПОЛУЧЕНИЯ ДАННЫХ ОТ ПИРОВ**
------------
## Установка LAMP - Transmission
* Устанавливаем Apache
  apt install apache2
  * Пример **/etc/apache2/ports.conf**:
    ```
    # If you just change the port or add more ports here, you will likely also
    # have to change the VirtualHost statement in
    # /etc/apache2/sites-enabled/000-default.conf
    Listen 80

    <IfModule ssl_module>
     Listen 443
    </IfModule>

    <IfModule mod_gnutls.c>
     Listen 443
    </IfModule>

    # vim: syntax=apache ts=4 sw=4 sts=4 sr noet
    ```
  * Основной конфиг **/etc/apache2/apache2.conf** (можно не менять)
  * Создаем конфиг сайта **/etc/apache2/sites-available/domain.conf**, пример содержимого:
    ```
    1
    2
    3
    4
    5
    6
    7
    8
    9
    10
    11
    12
    13
    14
    15
    16
    17
    18
    19
    20
    21
    22
    23
    24
    25
    26
    27
    28
    29
    30
    31
    32
    33
    34
    35
    36
    37
    38
    39
    40
    41
    42
    43
    44
    45
    46
    47
    48
    49
    50
    51
    52
    53
    54
    55
    56
    57
    58
    59
    60
    61
    62
    63
    64
    65
    66
    67
    68
    69
    70
    71
    72
    73
    74
    75
    76
    77
    78
    79
    80
    81
    82
    83
    84
    85
    86
    87
    88
    89
    90
    91
    92
    93
    94
    95
    96
    97
    98
    99
    100
    ServerName kravenrus.mykeenetic.net

    <VirtualHost *:80>
     # The ServerName directive sets the request scheme, hostname and port that
     # the server uses to identify itself. This is used when creating
     # redirection URLs. In the context of virtual hosts, the ServerName
     # specifies what hostname must appear in the request's Host: header to
     # match this virtual host. For the default virtual host (this file) this
     # value is not decisive as it is used as a last resort host regardless.
     # However, you must set it for any further virtual host explicitly.
     #ServerName www.example.com

     ServerAdmin kravenrus@gmail.com

     # Change the directory to your
     DocumentRoot /var/www/html

     # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
     # error, crit, alert, emerg.
     # It is also possible to configure the loglevel for particular
     # modules, e.g.
     #LogLevel info ssl:warn

     ErrorLog ${APACHE_LOG_DIR}/error.log
     CustomLog ${APACHE_LOG_DIR}/access.log combined

     # For most configuration files from conf-available/, which are
     # enabled or disabled at a global level, it is possible to
     # include a line for only one particular virtual host. For example the
     # following line enables the CGI configuration for this host only
     # after it has been globally disabled with "a2disconf".
     #Include conf-available/serve-cgi-bin.conf

     <Directory /var/www/>
      Options Indexes FollowSymLinks
      AllowOverride All
      Require all granted
     </Directory>

    </VirtualHost>

    # vim: syntax=apache ts=4 sw=4 sts=4 sr noet
    
    ```
