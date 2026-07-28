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

#

2)  Попасть в систему без пароля несколькими способами.
   Способ 1. init=/bin/bash
*  Запустить виртуальную машину и при выборе ядра для загрузки нажать e - в данном контексте edit. Попадаем в окно, где мы можем изменить параметры загрузки.
#
<img width="1245" height="876" alt="Снимок экрана от 2026-07-28 16-18-11" src="https://github.com/user-attachments/assets/57cedad7-ba44-40d6-8791-fd8b5e3bedd9" />
#
Добавляем в строку linux	/vmlinuz-5.4.0-216-generic root=/dev/mapper/ubuntu--vg-ubuntu--lv ro init=/bin/bash 
<img width="1245" height="876" alt="Снимок экрана от 2026-07-28 16-22-28" src="https://github.com/user-attachments/assets/b1c19df3-66b9-4112-aefa-49f5f35fa8f8" />
#
* Нажимаем Ctrl+X загружаемся в систему
<img width="1245" height="908" alt="Снимок экрана от 2026-07-28 16-25-18" src="https://github.com/user-attachments/assets/e4399cb6-d247-4d8c-9820-1949433b2ba2" />
#
* ОС теперь в режиме RO, Надо перементировать в RW сомандой и проврить на запись через создание файла

      mount -o remount,rw /
<img width="957" height="903" alt="Снимок экрана от 2026-07-28 16-31-38" src="https://github.com/user-attachments/assets/79ae75bb-062a-4aa7-83de-0df42ccb3985" />
# файлы успешно создались .

   Способ 2. Recovery mode
* В меню загрузчика на первом уровне выбрать второй пункт (Advanced options…), далее загрузить пункт меню с указанием recovery mode в названии. 
Получим меню режима восстановления
   
<img width="957" height="903" alt="image" src="https://github.com/user-attachments/assets/b9ddb552-b91e-43e3-aa63-6c87e2e96824" />
<img width="1172" height="979" alt="image" src="https://github.com/user-attachments/assets/ddece479-7029-4da6-866c-0ee6dc3b66ca" />

Далее выбираем пункт root и попадаем в консоль с пользователем root. Если вы ранее устанавливали пароль для пользователя root (по умолчанию его нет), то необходимо его ввести. 
В этой консоли можно производить любые манипуляции с системой.
<img width="1172" height="979" alt="image" src="https://github.com/user-attachments/assets/9f56e6c6-3697-4235-8363-02db93b57084" />
#
#
3) Установить систему с LVM, после чего переименовать VG
   Имется готовая ОС с лвм 
#
    root@ubuntu:~# vgs
    VG        #PV #LV #SN Attr   VSize   VFree 
    ubuntu-vg   1   1   0 wz--n- <11.25g <1.25g
#
  Переименуем VG ubuntu-vg в diectory. 
  
    root@ubuntu:~# vgrename ubuntu-vg diectory
    Volume group "ubuntu-vg" successfully renamed to "diectory"
#
  Далее правим /boot/grub/grub.cfg. Везде заменяем старое название VG на новое  diectory.
  
    export linux_gfx_mode
    menuentry 'Ubuntu' --class ubuntu --class gnu-linux --class gnu --class os $menuentry_id_option 'gnulinux-simple-c3f58731-e3ea-4b9f-aca4-ff6371d9985c' {
        recordfail
        load_video
        gfxmode $linux_gfx_mode
        insmod gzio
        if [ x$grub_platform = xxen ]; then insmod xzio; insmod lzopio; fi
        insmod part_gpt
        insmod ext2
        if [ x$feature_platform_search_hint = xy ]; then
          search --no-floppy --fs-uuid --set=root  44454575-c078-4e6c-8f31-20893975e678
        else
          search --no-floppy --fs-uuid --set=root 44454575-c078-4e6c-8f31-20893975e678
        fi
        linux   /vmlinuz-5.4.0-216-generic root=/dev/mapper/diectory-ubuntu--lv ro
        initrd  /initrd.img-5.4.0-216-generic
    }
    submenu 'Advanced options for Ubuntu' $menuentry_id_option 'gnulinux-advanced-c3f58731-e3ea-4b9f-aca4-ff6371d9985c' {
        menuentry 'Ubuntu, with Linux 5.4.0-216-generic' --class ubuntu --class gnu-linux --class gnu --class os $menuentry_id_option 'gnulinux-5.4.0-216-generic>
                recordfail
                load_video
                gfxmode $linux_gfx_mode
                insmod gzio
                if [ x$grub_platform = xxen ]; then insmod xzio; insmod lzopio; fi
                insmod part_gpt
                insmod ext2
                if [ x$feature_platform_search_hint = xy ]; then
                  search --no-floppy --fs-uuid --set=root  44454575-c078-4e6c-8f31-20893975e678
                else
                  search --no-floppy --fs-uuid --set=root 44454575-c078-4e6c-8f31-20893975e678
                fi
                echo    'Loading Linux 5.4.0-216-generic ...'
                linux   /vmlinuz-5.4.0-216-generic root=/dev/mapper/diectory-ubuntu--lv ro
                echo    'Loading initial ramdisk ...'
                initrd  /initrd.img-5.4.0-216-generic
        }
        menuentry 'Ubuntu, with Linux 5.4.0-216-generic (recovery mode)' --class ubuntu --class gnu-linux --class gnu --class os $menuentry_id_option 'gnulinux-5>
                recordfail
                load_video
                insmod gzio
                if [ x$grub_platform = xxen ]; then insmod xzio; insmod lzopio; fi
                insmod part_gpt
                insmod ext2
                if [ x$feature_platform_search_hint = xy ]; then
                  search --no-floppy --fs-uuid --set=root  44454575-c078-4e6c-8f31-20893975e678
                else
                  search --no-floppy --fs-uuid --set=root 44454575-c078-4e6c-8f31-20893975e678
                fi
                echo    'Loading Linux 5.4.0-216-generic ...'
                linux   /vmlinuz-5.4.0-216-generic root=/dev/mapper/diectory-ubuntu--lv ro recovery nomodeset dis_ucode_ldr
                echo    'Loading initial ramdisk ...'
                initrd  /initrd.img-5.4.0-216-generic
        }
      }
#
Настройку update-grub применять не нужно, сразу перезагружаем вм.
Ждем загрузку ОС и проверяем
<img width="1172" height="979" alt="image" src="https://github.com/user-attachments/assets/9cbaa863-873c-40fd-ba8d-9c6e78288204" />
# Ос успешноо загрузилась и видно новое именование VG
Переименуем еще для прикола LV ubuntu-lv в  sobaka.
<img width="1172" height="979" alt="image" src="https://github.com/user-attachments/assets/02c1a63d-96c3-4661-b616-60dce149e5fd" />
#
# Все успешно переименовалось и применилось.!
