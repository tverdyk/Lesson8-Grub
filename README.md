# Домашнее Задание
1)  Включить отображение меню Grub.
2)  Попасть в систему без пароля несколькими способами.
3)  Установить систему с LVM, после чего переименовать VG.

OS Ubuntu 20.04


1)  Включить отображение меню Grub.

* Для отображения меню нужно отредактировать конфигурационный файл. выставим 15 сек

    root@ubuntu:~# nano /etc/default/grub
    GRUB_DEFAULT=0
    #GRUB_TIMEOUT_STYLE=hidden
    GRUB_TIMEOUT=15
    GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
    GRUB_CMDLINE_LINUX_DEFAULT=""
    GRUB_CMDLINE_LINUX=""
#    
    root@ubuntu:~# update-grub2 
    Sourcing file `/etc/default/grub'
    Sourcing file `/etc/default/grub.d/init-select.cfg'
    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-5.4.0-216-generic
    Found initrd image: /boot/initrd.img-5.4.0-216-generic
    done
    root@ubuntu:~# reboot

* После перезагрузки можно  
  
