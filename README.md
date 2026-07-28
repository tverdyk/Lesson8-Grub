# Домашнее Задание
1)  Включить отображение меню Grub.
2)  Попасть в систему без пароля несколькими способами.
3)  Установить систему с LVM, после чего переименовать VG.

OS Ubuntu 20.04


1)  Включить отображение меню Grub.

* Для отображения меню нужно отредактировать конфигурационный файл. выставим 15 сек
#
    root@ubuntu:~# nano /etc/default/grub
    GRUB_DEFAULT=0
    #GRUB_TIMEOUT_STYLE=hidden
    GRUB_TIMEOUT=15
    GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
    GRUB_CMDLINE_LINUX_DEFAULT=""
    GRUB_CMDLINE_LINUX=""

* Обновить конфиг Grub
#    
    root@ubuntu:~# update-grub2 
    Sourcing file `/etc/default/grub'
    Sourcing file `/etc/default/grub.d/init-select.cfg'
    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-5.4.0-216-generic
    Found initrd image: /boot/initrd.img-5.4.0-216-generic
    done
    root@ubuntu:~# reboot

* Проверить что применилось можно в /boot/grub/grub.cfg, главный конфигурационный файл, генерируется автоматически. Увидим выставленное время set timeout=15. Ну и после перезагрузки увим меню загрузчика с отсчетом времени.
#
    root@ubuntu:~# cat /boot/grub/grub.cfg  | grep time
    set timeout=30
    if [ x$feature_timeout_style = xy ] ; then
      set timeout_style=menu
      set timeout=15  #Наше установленное время
    # Fallback normal timeout code in case the timeout_style feature is
      set timeout=15  #Наше установленное время
#
<img width="1245" height="876" alt="Снимок экрана от 2026-07-28 16-13-00" src="https://github.com/user-attachments/assets/f269d34f-b049-4546-886f-7ba1fa409ecc" />


2)  Попасть в систему без пароля несколькими способами.
