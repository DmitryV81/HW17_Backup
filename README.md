Домашняя работа № 17 Резервное копирование

Что нужно сделать?

Настроить стенд Vagrant с двумя виртуальными машинами: backup_server и client.

Настроить удаленный бекап каталога /etc c сервера client при помощи borgbackup. Резервные копии должны соответствовать следующим критериям:

    директория для резервных копий /var/backup. Это должна быть отдельная точка монтирования. В данном случае для демонстрации размер не принципиален, достаточно будет и 2GB;
    
    репозиторий дле резервных копий должен быть зашифрован ключом или паролем - на ваше усмотрение;
    
    имя бекапа должно содержать информацию о времени снятия бекапа;
    
    глубина бекапа должна быть год, хранить можно по последней копии на конец месяца, кроме последних трех. Последние три месяца должны содержать копии на каждый день. Т.е. должна быть правильно настроена политика удаления старых бэкапов;
    
    резервная копия снимается каждые 5 минут. Такой частый запуск в целях демонстрации;
    
    написан скрипт для снятия резервных копий. Скрипт запускается из соответствующей Cron джобы, либо systemd timer-а - на ваше усмотрение;
    
    настроено логирование процесса бекапа. Для упрощения можно весь вывод перенаправлять в logger с соответствующим тегом. Если настроите не в syslog, то обязательна ротация логов.

    Запустите стенд на 30 минут.
    
    Убедитесь что резервные копии снимаются.
    
    Остановите бекап, удалите (или переместите) директорию /etc и восстановите ее из бекапа.

Ход работы:

В Vagrant разворачиваем 2 ВМ backup_server и client. Настройка borg происходит в автоматическом режиме с использование ansible.

Пароль для архива: Otus1234

Проверяем бэкапы:
```
[root@client ~]# borg list borg@192.168.11.160:/var/backup/
Remote: Warning: Permanently added '192.168.11.160' (ECDSA) to the list of known hosts.
Enter passphrase for key ssh://borg@192.168.11.160/var/backup: 
etc-2023-03-11_16:27:11              Sat, 2023-03-11 16:27:12 [aeaf11266405c565c54d9f3160d50cbf27a6e006c21f2b7a860b7548ea7db5c6]
etc-2023-03-11_16:33:06              Sat, 2023-03-11 16:33:07 [328f0d81dc07043099b98146f2d36af059524cc57f11f9cba2d3d364e203f4ed]
etc-2023-03-11_16:39:06              Sat, 2023-03-11 16:39:07 [f1c62786df55984459e43769c114f2196782addd03af8160aa0613c7d4bfae0c]
etc-2023-03-11_16:45:06              Sat, 2023-03-11 16:45:07 [48ec7dd87d4a5319f275a626256013c0e2bc015b26ec6f3af94298241454cc53]

```

Восстанавливаем файл hostname:
```
[root@client ~]# ll  
total 16
-rw-------. 1 root root 5570 Apr 30  2020 anaconda-ks.cfg
-rw-------. 1 root root 5300 Apr 30  2020 original-ks.cfg
[root@client ~]# borg extract borg@192.168.11.160:/var/backup/::etc-2023-03-11_16:39:06 etc/hostname
Remote: Warning: Permanently added '192.168.11.160' (ECDSA) to the list of known hosts.
Enter passphrase for key ssh://borg@192.168.11.160/var/backup: 
Warning: File system encoding is "ascii", extracting non-ascii filenames will not be supported.
Hint: You likely need to fix your locale setup. E.g. install locales and use: LANG=en_US.UTF-8
[root@client ~]# ll
total 16
-rw-------. 1 root root 5570 Apr 30  2020 anaconda-ks.cfg
drwx------. 2 root root   22 Mar 11 16:46 etc
-rw-------. 1 root root 5300 Apr 30  2020 original-ks.cfg
[root@client ~]# ll etc/
total 4
-rw-r--r--. 1 root root 7 Mar 11 16:25 hostname
[root@client ~]# 

```
