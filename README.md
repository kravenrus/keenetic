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
  * Основной конфиг **/etc/apache2/apache2.conf** (можно не менять)
  * Создаем конфиг сайта **/etc/apache2/sites-available/domain.conf**, пример содержимого:
  hjh
