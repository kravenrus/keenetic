# Keenetic OS
#### Войти в CLI с логином и паролем из веб-интерфейса по адресу - 192.168.1.1:22
* Отключить прослушку :80 порта: `no ip http easy-access`
* Отключить прослушку :443 порта: `no ip http ssl enable`
* Сменить порт веб-интерфеса: `ip http port PORT`

## Установить Debian 10
#### Дистрибутив можно взять [**здесь**](http://ndm.zyxmon.org/binaries/debian/)
* Форматировать накопитель в **EXT4** и подключить к роутеру
* Через веб-интерфейс роутера **дать общий доступ** к подключенному накопителю и **подключиться** к нему
* Создать папку **install** и поместить в нее дистрибутив формата **.tar.gz**
#### Войти в Debian с логином: _root_ и паролем: _debian_ по адресу - 192.168.1.1:222
* Добавление пользователя с правами root: `useradd -ou 0 -g 0 USERNAME`
* Создание пароля для нового пользователя: `passwd USERNAME`
* Сменить пароль: `passwd`
* Обновить пакеты: `apt update`
* Установка пакетов: `apt install PKG`
  * Набор сетевых инструментов - **net-tools**
  * Многофункциональный диспетчер файлов - **mc**
  * Взаимодействие с серверами по протоколам с синтаксисом URL - **curl**

## Minidlna - медиа сервер
`apt install minidlna`
#### Настройка Minidlna:
* Основные параметры **/etc/minidlna.conf**:
  ```
  media_dir=/var/lib/minidlna
  merge_media_dirs=yes
  root_container=B
  friendly_name=Name
  ```
  [Подробная информация по настройке конфига](http://itadept.ru/linux-dlna-server-minidlna/ "Подробная информация по настройке конфига")
#### Запуск Minidlna: `/etc/init.d/minidlna start`
* ###### Для запуска службы вместе с Debian добавляем строку в конец файла chroot-services.list
  ```
  minidlna
  ```

## Transmission - Bittorrent клиент
`apt install transmission-daemon`
#### Настройка Transmission:
* Сменить пользователя в **/etc/init.d/transmission-daemon**:
  ```
  USER=root
  ```
  ###### Либо создать пользователя, дать права и сменить на значение его имени
#### Запуск Transmission: `/etc/init.d/transmission-daemon start`
* ###### Для запуска службы вместе с Debian добавляем строку в конец файла chroot-services.list
  ```
  transmission-daemon
  ```
* Останавливаем службу и изменяем **/etc/transmission-daemon/settings.json**:
  ```
  "bind-address-ipv4": "192.168.1.1"
  "rpc-password": "password"
  "rpc-username": "username"
  "rpc-port": 9091,
  "rpc-whitelist-enabled": false,
  ```
  ###### Также настраивается через веб-интерфейс и Transmission Qt Client после первой инициализации
  [Подробная информация по настройке конфига](https://pcminipro.ru/os/nastrojka-transmission-daemon-settings-json/ "Подробная информация по настройке конфига")
* Снова запускаем службу
#### НЕ ЗАБЫВАЕМ ПРОБРОСИТЬ ПОРТ ИЗ ПАРАМЕТРА "peer-port" ДЛЯ ПОЛУЧЕНИЯ ДАННЫХ ОТ ПИРОВ

## LAMP
### Apache
`apt install apache2`
#### Настройка
* Проверка портов: `netstat -ltup` **(должен быть установлен net-tools)**
* Основной конфиг **/etc/apache2/apache2.conf** (можно не менять)
* Пример **/etc/apache2/ports.conf**:
  ```
  Listen 80

  <IfModule ssl_module>
    Listen 443
  </IfModule>

  <IfModule mod_gnutls.c>
    Listen 443
  </IfModule>
  ```
* Создаем конфиг сайта **/etc/apache2/sites-available/example.com.conf**, пример содержимого:
  ```
  ServerName example.com

  <VirtualHost *:80>

    #ServerName example.com
    ServerAdmin example@example.com
    DocumentRoot /mnt/example

    #LogLevel info ssl:warn

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    <Directory /mnt/example/>
      Options FollowSymLinks
      AllowOverride All
      Require all granted
    </Directory>

    AddDefaultCharset UTF-8

  </VirtualHost>
  ```
* Для редиректа с другого домена добавляем строки:
  ```
  RewriteEngine On
  RewriteCond %{HTTP_HOST} ^example\.net$ [NC]
  RewriteRule ^(.*)$ http://example.com$1 [R=301,L]
  ```
* Для включения доступа по паролю: `htpasswd .htpasswd vasya`
  ```
  <Directory "/var/www/example.com/admin">
    AuthType Basic
    AuthName "Administrative zone"
    AuthUserFile /var/www/example.com/admin/.htpasswd
    Require valid-user
  </Directory>
  ```
#### Запустить Apache `/etc/init.d/apache2 start`
* Перед запуском возможно понадобится:
  * Отключить ненужные сайты из дериктории **/etc/apache2/mods-enabled**: `a2dissite 000-default.conf`
  * Включить нужные сайты из дериктории **/etc/apache2/sites-available**: `a2ensite example.com.conf`
  * Включить модуль **Rewrite** для Apache: `a2enmod rewrite` (**обязательно если настроен редирект**)
* ###### Для запуска службы вместе с Debian добавляем строку в конец файла chroot-services.list
  ```
  apache-htcacheclean
  apache2
  ```

### PHP
`apt install php libapache2-mod-php`
#### Настройка
* Изменяем приоритет index.php в **/etc/apache2/mods-enabled/dir.conf** (опционально):
  ```
  <IfModule mod_dir.c>
    DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
  </IfModule>
  ```
#### Перезапуск Apache `/etc/init.d/apache2 restart`
* Дополнительно:
  * Пример **index.php** для проверки:
    ```
    <?php
      phpinfo();
    ?>
    ```

### MariaDB
`apt install mariadb-server php-mysql`
#### Запускаем MariaDB `/etc/init.d/mysql start`
#### Настройка `mariadb`
```
GRANT ALL ON *.* TO 'USERNAME'@'localhost' IDENTIFIED BY 'PASSWORD' WITH GRANT OPTION;
FLUSH PRIVILEGES;
exit
```
* ###### Для запуска службы вместе с Debian добавляем строку в конец файла chroot-services.list
  ```
  mysql
  ```

### phpmyadmin
`apt install phpmyadmin`
###### Во время установки производится интуитивно понятная настройка - _читаем что выводится на экране_
#### Перезапускаем Apache `/etc/init.d/apache2 restart`
* phpmyadmin работает по адресу типа: `http://example.com/phpmyadmin`

### SSL
`apt install certbot python-certbot-apache`
#### Команды:
* Для получения и установки сертификатов: `certbot --apache`
* Только для получения сертификатов: `certbot certonly --apache`
* Удаление информации с серверов letsenrypt: `certbot revoke --cert-path /etc/letsencrypt/live/example.com/cert.pem`
* Удаление сертификатов и всех символик с локального сервера: `certbot delete --cert-name example.com`
* Обновление сертификатов: `certbot renew --dry-run` (возможно нет)
#### НЕ ЗАБЫВАЕМ ПРОБРОСИТЬ ПОРТ :80 ДЛЯ ПОЛУЧЕНИЯ СЕРТИФИКАТА
###### Получение и установка сертификатов интуитивно понятная - _читаем что выводится на экране_
#### Перезапуск Apache `/etc/init.d/apache2 restart`
