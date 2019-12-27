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
