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

## Minidlna - медиа сервер
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
  * #### Запускаем Minidlna: `/etc/init.d/minidlna start`
    * ###### Для запуска службы вместе с Debian добавляем строку в конец файла chroot-services.list
      ```
      minidlna
      ```

## Transmission - Bittorrent клиент
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
* #### Запускаем Transmission: `/etc/init.d/transmission-daemon start`
  * ###### Для запуска службы вместе с Debian добавляем строку в конец файла chroot-services.list
    ```
    transmission-daemon
    ```
* **НЕ ЗАБЫВАЕМ ПРОБРОСИТЬ ПОРТ ИЗ ПАРАМЕТРА "peer-port" ДЛЯ ПОЛУЧЕНИЯ ДАННЫХ ОТ ПИРОВ**

## LAMP
* ### Apache
  `apt install apache2`
  * #### Настраиваем Apache
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
    * Основной конфиг **/etc/apache2/apache2.conf** (можно не менять)
    * Создаем конфиг сайта **/etc/apache2/sites-available/domain.conf**, пример содержимого:
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
      * Для редиректа добавляем строки
        ```
        RewriteEngine On
        RewriteCond %{HTTP_HOST} ^example\.net$ [NC]
        RewriteRule ^(.*)$ http://example.com$1 [R=301,L]
        ```
    * Отключаем ненужные сайты из дериктории **/etc/apache2/mods-enabled**: `a2dissite 000-default.conf`
    * Включаем нужные сайты из дериктории **/etc/apache2/sites-available**: `a2ensite example.com.conf`
    * Если нужно включаем модуль **Rewrite** для Apache: `a2enmod rewrite`
  * #### Запускаем Apache `/etc/init.d/apache2 start`
* ### PHP
  `apt install php libapache2-mod-php`
  * Изменяем приоритет index.php в **/etc/apache2/mods-enabled/dir.conf** (опционально):
    ```
    <IfModule mod_dir.c>
      DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
    </IfModule>
    ```
  * #### Перезапускаем Apache `/etc/init.d/apache2 restart`
  * Пример **index.php** для проверки:
    ```
    <?php
      phpinfo();
    ?>
    ```
* ### MariaDB
  `apt install mariadb-server php-mysql`
  * Запускаем MariaDB `/etc/init.d/mysql start`
  * Переходим к настройке в SSH:
    ```
    mariadb

    GRANT ALL ON *.* TO 'USERNAME'@'localhost' IDENTIFIED BY 'PASSWORD' WITH GRANT OPTION;
    FLUSH PRIVILEGES;
    exit
    ```
* ### phpmyadmin
  `apt install phpmyadmin`
  ###### Тут же и производится интуитивно понятная настройка
  * #### Перезапускаем Apache `/etc/init.d/apache2 restart`
  * phpmyadmin работает по адресу типа: `http://example.com/phpmyadmin`
* ### SSL
  `apt install certbot python-certbot-apache`
    * Для получения и установки сертификатов: `certbot --apache`
  * Только для получения сертификатов: `certbot certonly --apache`
  * Удаление информации с серверов letsenrypt: `certbot revoke --cert-path /etc/letsencrypt/live/kravenrus.mykeenetic.net/cert.pem`
  * Удаление сертификатов и всех символик с локального сервера: `certbot delete --cert-name kravenrus.mykeenetic.net`
  * Обновление сертификатов: `certbot renew --dry-run` (возможно нет)
