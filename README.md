# Автоматизированный Linux с нуля 10.0 (SysV)

Протестировано с Debian 10 Buster.

*Этот проект основан на официальной книге LFS 10.0

http://lfs.mirror.fileplanet.com/lfs/view/10.0/

## Требования к пакету

Добавьте следующие пакеты 

```
sudo apt install build-essential bison gawk git htop texinfo
```

Установите эти переменные среды

```bash
export LFS=/mnt/lfs
export ALFS=/mnt/lfs/alfs
```

## Раздел LFS

| : стирает диск / dev / sdb |
| --- |

Создайте новый раздел ext4 /dev / sdb1 (опция n) и сделайте его загрузочным (опция a)

```bash
sudo fdisk /dev/sdb
```

Смонтировать новый раздел ext4

```bash
sudo mkdir $LFS
sudo mount -t ext4 /dev/sdb1 $LFS
```

## Подготовить хост

Возьмите файлы проекта alfs

```bash
sudo git clone https://github.com/mattwind/alfs.git $ALFS
cd $ALFS
```

Проверка необходимых программ

`sudo $ALFS/version_check.sh`

Загрузите исходный код цепочки инструментов из wget-list

`sudo -E $ALFS/get_packages.sh`

Настройка пользовательской среды LFS

`sudo -E $ALFS/useradd_lfs.sh`

## Создание набора инструментов

Эти скрипты необходимы для запуска от имени нового пользователя lfs

```bash
sudo su lfs 
$ALFS/scripts/build_toolchain.sh
$ALFS/scripts/build_temp-toolchain.sh
exit
```

## Сборка системы LFS

Запуск от имени root с переменными среды, установленными ранее

```bash
sudo -E $ALFS/scripts/build_lfs.sh
```

## Загрузчик Grub

Ниже приведено предупреждение о том, как я установил grub, находясь внутри укорененной системы LFS

Подумайте о том, чтобы прочитать книгу LFS для резервного копирования вашего загрузчика.

http://lfs.mirror.fileplanet.com/lfs/view/stable/chapter10/grub.html

| : убедитесь, что вы находитесь в chroot|
| --- |


```bash
grub-install --root-directory=/ /dev/sdb
grub-mkconfig -o /boot/grub/grub.cfg
```

### Настройки  Tweaks

Обновление настроек grub по умолчанию

`vi /etc/default/grub`

Получите понятные сетевые имена eth0 и консоль qemu при загрузке.

```bash
GRUB_TERMINAL=console
GRUB_CMDLINE_LINUX_DEFAULT="net.ifnames=0 biosdevname=0"
```

Не забудьте запустить grub-mkconfig, чтобы применить новые настройки grub по умолчанию.

```bash
grub-mkconfig
```

## Дополнительные скрипты

Убедитесь, что заданы переменные среды LFS и ALFS и что раздел LFS смонтирован.

Повторно введите chroot и передайте раздел

```bash
sudo -E $ALFS/extras/chroot.sh /dev/sdb1
```

Эмулируйте систему LFS с помощью qemu (pass drive)

```bash
sudo -E $ALFS/extras/qemu.sh /dev/sdb
```

## Примечания

Пароль root в системе с привязкой к LFS - root.

### LFS Пользователь

Пользователь lfs в хост-системе может быть удален с sudo deluser lfsпомощью папки lfs user /home / lfs, которую также можно удалить. Требуется только создать первый набор инструментов.

### Kernel Panic

Это может произойти, если вы пытаетесь загрузиться из qemu, а запись для root=/dev/???не является sda

Просто отредактируйте vi /boot/grub/grub.cfgи измените корневые ссылки на sda1

```bash
root=/dev/sda1
```

Когда я загружаюсь со своего физического сервера, мне пришлось установить его обратно на sdb1, потому что sda - моя основная установка Debian.

```bash
root=/dev/sdb1
```

### Ссылки

*Linux с нуля 10 Книга*

http://lfs.mirror.fileplanet.com/lfs/view/10.0/

*За пределами Linux из книги Scrach 10*

http://lfs.mirror.fileplanet.com/blfs/view/10.0/

*Добавлены сжатые одностраничные версии в каталог книг*

