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
