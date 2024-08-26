# Dual boot

## Немного теории

Познакомимся с терминами `boot mode`, `drive (disk) partition`, `boot partition`, `system partition`, `файловая система`.


### Boot Mode

`boot mode` --- алгоритм, определяющий тип загрузки операционной системы в память.
Разновидности `boot mode`:

- `BIOS` (Basic Input/Output System)--- использовался раньше
- `UEFI` (EFI, ESP, Unified Extensible Firmware Interface) --- на современных компьютерах, замена BIOS

`UEFI BIOS` поддерживает два режима загрузки: режим загрузки `Legacy BIOS` и режим загрузки `UEFI`. Обратная совместимость нужна для того, что некоторые устройства и операционные системы пока не поддерживают `UEFI` на основе `BIOS` и могут загружаться только с режиме загрузки `Legacy BIOS`.

Стадии загрузки ПК:

- Когда мы нажимаем на кнопку включения, первым делом подаётся электричество на процессор.

- В оперативной памяти на данный момент ничего нет (она является энергозависимой памятью), поэтому процессор выполняет инструкции из прошивки микросхемы на материнской плате. Прошивка микросхемы --- это набор бинарных данных в ПЗУ (энергонезависимая память для хранения неизменяемых данных), который соответствует некоторой программе и служит для того, чтобы сделать опрос устройств (встроенного программного обеспечения: клавиатура, мышь, флешка) и определить запоминающие устройства с загрузчиками ОС (и много чего ещё, смотри `POST` --- `Power-On Self-Test`), которым потом можно передать управление.
  
  Эти бинарные данные расположены в памяти, начиная со специального адреса, на который прыгает процессор при включении. Данные на микросхему записываются на заводе-производителе материнской платы с помощью специального устройства, называемого программатором.

- Управление (в случае `MBR`) передаётся на небольшой загрузчик, расположенный в первом секторе загрузочного диска (обычно размером 512 байт).

- Далее запускается более умный загрузчик (в нашем случае, это будет `Grub`), способный загрузить ОС. Либо происходит многоэтапная загрузка: каждый раз подгружается всё более умный загрузчик, пока не появится тот, который в состоянии загрузить систему.

- Загрузчик загружает ядро в память и там запускается процесс init.

Больше информации:

- [wiki, Начальная загрузка компьютера](https://ru.wikipedia.org/wiki/%D0%9D%D0%B0%D1%87%D0%B0%D0%BB%D1%8C%D0%BD%D0%B0%D1%8F_%D0%B7%D0%B0%D0%B3%D1%80%D1%83%D0%B7%D0%BA%D0%B0_%D0%BA%D0%BE%D0%BC%D0%BF%D1%8C%D1%8E%D1%82%D0%B5%D1%80%D0%B0)
- Хорошая статья про загрузку `linux` (vmlinuz, initrd, ...) с картинкой: [sysadminium.ru](https://sysadminium.ru/adm_serv_linux-boot_algorithm/)

### Drive (disk) partitions (разделы диска)

Для удобства использования дисков, их делят на разделы. A partition is a logical division of a hard disk drive (HDD) that's treated as a separate unit by operating systems (OSes) and file systems. OSes and file systems manage information on each partition as if it's a distinct hard drive.

Информация о том, как диск поделён на разделы (с какого адреса они начинаются), хранится в самом первом загрузчике. Есть несколько стилей таблицы разделов жёстких дисков:

- `MBR` --- устаревший, использовался в `BIOS`. Имеет много минусов. Например, может работать только с дисками размером до 2TB и имеет ограниченное количество (логических) разделов. Его единственное преимущество --- более широкая совместимость: работает и с `BIOS`, и с `UEFI`. При этом `BIOS` может работать только с `MBR`.
- `GPT` --- новый, используется в `UEFI`. Имеет много преимуществ над `MBR`, но не умеет работать с `BIOS`.

Раньше диски разбивались на разделы с помощью `MBR`, при этом диск не мог быть больше 2 TB, а разделов не могло быть больше четырёх. При этом таблица разделов и первичный загрузчик помещались на первый сектор диска в `MBR`. А `GPT` позволяет использовать очень большие диски и помещает загрузчик на специальный раздел, а также позволяет создавать очень много разделов.

Диск разбитый на разделы с помощью таблицы разделов `GPT` в первом секторе продолжает хранить `MBR` запись. Это делается для того, чтобы старые системы с `BIOS` могли использовать `GPT` диски.

`UEFI` не использует загрузчик из `MBR`, вместо этого используется загрузчик со специального раздела на диске, который отформатирован в `FAT32` и называется `ESP` или `EFI`. Обычно это файл `/efi/boot/bootx64.efi`.

### Boot partition (загрузочный раздел)

The boot partition is a primary partition that contains the boot loader, a piece of software responsible for booting the operating system. For example, in the standard Linux directory layout (Filesystem Hierarchy Standard), boot files (such as the kernel, initrd, and boot loader GRUB) are mounted at `/boot/`.

### System partition (системны раздел)

The system partition is the disk partition that contains the operating system folder, known as the system root. By default, in Linux, operating system files are mounted at `/` (the root directory).

### Файловая система

`FAT32`, `NTFS`, `ext4` --- файловые системы. Каждый раздел имеет свою файловую систему.

### Работа с разделами в Ubuntu

Для работы лучше всего использовать утилиту `GParted` (имеет GUI).

Доступные запоминающие устройства (диски) доступны как блочные устройства в папке `/dev`. Чтобы
получить к ним доступ, нужно примонтировать их себе в систему.

Пояснение, что такое `NVMe` и `sda`: [www.quora.com](https://www.quora.com/I-have-a-new-laptop-My-disc-is-not-dev-sda-but-dev-nvmeOn1-Why-is-that). Если кратко:

- `NVMe` --- протокол (интерфейс) для доступа к SSD диску через PCIe.
- `nvme0n1` --- наименование диска, подключённого по интерфейсу `NVMe` (обычно SSD, отображается в `lsblk`)
- `sda` --- наименование диска, подключённого по интерфейсу `SATA` (обычно HDD, отображается в `lsblk`)

Если вы подключаете сырой `hdd диск`, то он будет отображаться как `sdX`, где `X` --- буква латинского алфавита. В таком случае память на диске не аллоцирована (можно посмотреть в `Gparted`). Чтобы начать пользоваться диском и что-либо на нём хранить, нужно создать разделы. Это можно сделать с помощью утилиты `GParted` (ни в коем случае не использовать утилиты `Windows`, если на ПК стоит dual-boot с линуксом). Разделы будут отображаться как `sda1` или `nvme0n1p1`.


## Как зайти в BIOS и Boot Menu

`HP`:

- `F10` (or `esc`) --- BIOS (здесь можно поменять приоритет загрузки --- с флешки/диска/сети...)
- `F9` --- Boot Menu --- здесь можно выбрать операционную систему, в которую загружаешься

`Lenovo`:

- `F2` (or `Fn+F2`) --- BIOS
- `F12` --- Boot Menu


## Dual boot installation

**Important:**  After installation, find and save manuals with the model and technical specifications of the laptop in the driver folder.

- `Windows 11` installation from USB Flash: [remontka.pro](https://remontka.pro/windows-11-install/)
- `Ubuntu` installation near Windows: [opensource.com](https://opensource.com/article/18/5/dual-boot-linux)
- Full guide: [nix.ru](https://www.nix.ru/computer_hardware_news/hardware_news_viewer.html?id=210175)
- Inside file `/etc/default/grub` change first block of variables (after `If your computer has multiple ...`) with the following:

```text
### Uncomment 2 last lines to boot from the previous OS
#GRUB_DEFAULT=0
GRUB_DEFAULT=saved
GRUB_SAVEDEFAULT=true

GRUB_TIMEOUT_STYLE=hidden
GRUB_TIMEOUT=10
### Wait indefinitely
#GRUB_TIMEOUT=-1

#GRUB_DISTRIBUTOR=`( . /etc/os-release; echo $HOSTNAME ${NAME:-Ubuntu} $VERSION ) 2>/dev/null || echo Ubuntu`
GRUB_DISTRIBUTOR=$( ( . /etc/os-release; echo $HOSTNAME ${NAME:-Ubuntu} $VERSION ) 2>/dev/null || echo "Ubuntu (error in GRUB_DISTRIBUTOR)" )

GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
GRUB_CMDLINE_LINUX=""
```

Then run command:

```bash
sudo update-grub
reboot
```

More:

- `OPTIONAL` Change OS name in grub: [just your text](https://askubuntu.com/a/759606) or [add your nick to serial number](https://askubuntu.com/a/1308185). In short:

```bash
# edit `/etc/default/grub`: change that line
GRUB_DISTRIBUTOR=$( ( . /etc/os-release; echo $HOSTNAME ${NAME:-Ubuntu} $VERSION ) 2>/dev/null || echo "Ubuntu (error in GRUB_DISTRIBUTOR)" )

# edit `/etc/grub.d/05_debian_theme`: comment case in set_default_theme

# save changes, back to terminal
sudo update-grub
reboot
```

- `OPTIONAL` Change default system to load: change `GRUB_DEFAULT` to (for example) "1>1" to enter recovery mode ([more](https://www.fosslinux.com/113044/the-ultimate-guide-to-recovery-and-rescue-mode-in-ubuntu.htm))


## Change system with grub installation

Boot that system and run in terminal:

```bash
sudo grub-install /dev/sdX # X --- where / is mounted, it may be /dev/nvme0n1
sudo update-grub
```

## Mount `Windows` partitions

Change `/etc/fstab` with the following:

```text
### Windows, disk C
/dev/sda3 /mnt/Windows/C ntfs ro,noload 0 0

### Windows, disk D
/dev/sda4 /mnt/Windows/D ntfs defaults 0 0
```

Reload system to check `fstab` configuration.

```bash
sudo systemctl daemon-reload
```


### Слетел grub (starts in command line, recovery mode)

Есть 2 варианта. Если ubuntu слетела и нет никакой другой, с которой можно загрузиться --- тогда нужно заново размечать диск и ставить ubuntu с флешки, grub установится автоматически. Если есть доступная ubuntu, то последовательность действий такая ([stackoverflow](https://unix.stackexchange.com/questions/329926/grub-starts-in-command-line-after-reboot/330852#330852)):

- Найти диск, на котором есть папка `boot/grub` (с помощью `ls (hd0,gpt<number>)).
- Выполнить с этим диском команды:

```bash
set prefix=(hd0,gpt6)/boot/grub
set root=(hd0,gpt6)
insmod linux
insmod normal
normal
# automatic boot, choose boot to linux, then in terminal
sudo grub-install /dev/sdX # X --- where / is mounted, it may be /dev/nvme0n1
sudo update-grub
```

```bash
grub-install --root-directory=/<mounted> /dev/sda
```


### Копия Ubuntu с помощью `Clonezilla`

Для клонирования будем использовать `Clonezilla`. Подробный видео-гайд по тому, как создать образ и как восстановиться с этого образа: [youtube](https://www.youtube.com/watch?v=QfdiIeKUFNs).

P.S. Для создания образа `Clonezilla` потребуется Windows (`rufus` есть только на неё) и флешка. Также нужен ещё съёмный диск, куда можно будет загрузить образ системы (он загружается в папку, так что стирать всё на диске не нужно).

**Отличия от видео:** нам нужно сделать копию/восстановление раздела, а не всего диска. Поэтому выбираем `saveparts` и `restoreparts` вместо `savedisk` и `restore disk` соответственно.

Не всё так гладко, как могло бы быть. После восстановления из копии по инструкции из видео нужно поправить 2 вещи:

- `/etc/fstab` --- нужно поменять `UUID` загрузчика и корня при необходимости
- `grub` --- нужно сконфигурировать его под свой `UUID`

Для этого:

- загружаемся с флешки (Live CD/USB)

- Если мы ставим копию на тот же ПК, где стоит оригинальная система, то нам нужно сгенерировать **новый** `UUID` для восстановленного раздела. Для этого:

```bash
sudo e2fsck -f /dev/nvme0n1p<num> # else getting "This operation requires a freshly checked filesystem"
sudo tune2fs -U random /dev/nvme0n1p<num>
sudo blkid # check that UUID has changed
```

Чтобы найти `<num>` раздела, можно зайти в `GParted` или посмотреть вывод команды `sudo lsblk`.

- Монтируем раздел с восстановленной Ubuntu в папку `/mnt`:

```bash
sudo mount /dev/nvme0n1p<num> /mnt
```

- Изменяем корневой `UUID` в файле `/mnt/etc/fstab` на новый. Новый `UUID` можно найти с помощью `sudo blkid`. Если ubuntu восстанавливается на другом ПК, то нужно также поменять `UUID` раздела загрузки `EFI System` (как его найти, смотри дальше в инструкции, но исправить его нужно на этом шаге). Получится должно нечто следующее:

```text
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/nvme0n1p6 during curtin installation
/dev/disk/by-uuid/32b8f18e-96ed-4898-b1c4-e0123255e620 / ext4 defaults 0 1
# /boot/efi was on /dev/nvme0n1p1 during curtin installation
/dev/disk/by-uuid/E472-420F /boot/efi vfat defaults 0 1
/swap.img       none    swap    sw      0       0
```

- Осталось разобраться с `grub`. Можно воспользоваться [этой](https://askubuntu.com/questions/6317/how-can-i-install-windows-after-ive-installed-ubuntu/6321#6321) инструкцией, можно сделать вариант ниже (он точно работает).

- В папку `/mnt` пробрасываем папки, файлы в которых появляются во время работы `Ubuntu`, чтобы потом можно было сделать `chroot`:

```bash
sudo -i
### One-shot
for d in /dev /proc /sys; do mount --rbind $d /mnt$d; done

### Simpler version
#mount --rbind /dev /mnt/dev
#mount --rbind /proc /mnt/proc
#mount --rbind /sys /mnt/sys
```

- Также нужно примонтировать раздел загрузки (для `UEFI`) в папку `/boot/efi`. Чтобы его найти, нужно выполнить команды:

```bash
sudo fdisk -l
```

Вывод примерно следующий:

```text
Disk /dev/nvme0n1: 476.94 GiB, 512110190592 bytes, 1000215216 sectors
Disk model: SAMSUNG MZVLB512HBJQ-000H1              
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 6CBC3BD7-2FA3-4A13-9F0A-439C6AA249C5

Device             Start        End   Sectors   Size Type
/dev/nvme0n1p1      2048     206847    204800   100M EFI System
/dev/nvme0n1p2    206848     239615     32768    16M Microsoft reserved
/dev/nvme0n1p3    239616  580376575 580136960 276.6G Microsoft basic data
/dev/nvme0n1p4 785176576  846616575  61440000  29.3G Microsoft basic data
/dev/nvme0n1p5 989976576 1000214527  10237952   4.9G Microsoft basic data
/dev/nvme0n1p6 580376576  682776575 102400000  48.8G Linux filesystem
/dev/nvme0n1p7 682776576  785176575 102400000  48.8G Linux filesystem
/dev/nvme0n1p8 846616576  989976575 143360000  68.4G Linux filesystem
```

Нас интересует раздел `EFI System`. Ему соответствует устройство `/dev/nvme0n1p1`, поэтому:

```bash
sudo mount /dev/nvme0n1p1 /mnt/boot/efi
```

- Делаем `chroot` и устанавливаем grub:

```bash
chroot /mnt
grub-install
update-grub
reboot
```

Если появляется ошибка, то выходим из `chroot` и устанавливаем следующие пакеты и прописываем полностью аргументы для `grub-install`:

```bash
### for different target see folders in /usr/lib/grub, default may be i386-pc
#sudo apt-get install grub-pc grub-efi grub-common grub-customizer
grub-install --target x86_64-efi /dev/nvmr0n1 # device --- disk, where "EFI System" is located
```

Перезагружаем систему и всё должно работать.

После этого, в случае существования нескольких разделов с `Ubuntu` на ПК, может понадобиться перенести `grub` на предыдущую `Ubuntu`. Для этого:

- Если есть возможность зайти с желаемого раздела:
  - Зайти в терминал и выполнить

```bash
# Next line is required only if /etc/fstab 
#sudo mount # grub-install searches for EFI in /boot/efi by default
sudo grub-install
sudo update-grub
reboot
```

- Если заходить с `USB`:
  - Примонтировать разделы в `/mnt` и выполнить действия выше.

Текущую конфигурацию `grub` можно посмотреть командой `less /boot/grub/grub.cfg`

Файлы:

- `/etc/default/grub`
- `/boot/grub/grub.cfg` --- generates automatically
- `/usr/lib/grub/i386-pc/modinfo.sh` --- после установки может быть нужно скопировать папку `i386-pc` с Live USB.

Если не видно `Windows`:

- Подключить `os-prober`:

  ```bash
  sudo os-prober
  ```

  Аналогично раскомментировать строку в `/etc/default/grub`:

  ```text
  #GRUB_DISABLE_OS_PROBER=false
  ```

  Load Windows from grub command line: [unix.stackexchange.com](https://unix.stackexchange.com/questions/259069/how-to-start-a-windows-partition-from-the-grub-command-line).

  Setup of `grub2`: [help.ubuntu.com](https://help.ubuntu.com/community/Grub2/Setup).

- Если написанное выше не помогло, то нужно руками добавить `Windows` в `grub`, изменяя файл `/etc/grub.d/40_custom`: [askubuntu.com](https://askubuntu.com/questions/1425637/how-can-i-add-windows-11-to-grub-menu).


## Перенос Installed Ubuntu -> VMWare

### Clonezilla

Для переноса нужны:

- Флешка, на которой установлен образ `Clonezilla` с помощью `rufus`.
- ISO образ `Clonezilla`.
- Съёмный диск, на который можно сохранить образ системы.
- Образ `Ubuntu` (нужен будет только для редактирования настроек переносимой системы).

0) Перед тем, как переносить, рекомендуется сначала сделать систему как можно меньше, используя `e2fsck -f` then `resize2fs -M`: [askubuntu.com](https://askubuntu.com/questions/270407/how-do-i-migrate-currently-installed-ubuntu-to-a-virtual-machine).

1) Подключаем флешку c `Clonezilla` (созданную из её `iso` образа через `rufus`) и создаём образ, как описано в видео: [youtube](https://www.youtube.com/watch?v=qbz6H_x4d50) (Похожее видео: [youtube](https://www.youtube.com/watch?v=QfdiIeKUFNs)). Далее работаем с виртуалкой и в основном следуем гайду, расположенному тут: [youtube](https://www.youtube.com/watch?v=U0ZAmyFxZvE). Опишем подробнее:

2) Создаём виртуалку под `Linux`. При создании выбираем пункт `установить OS позже` вместо установки из `ISO`. Памяти выделяем чуть больше (1-2GB), чем было установленной системе. Также при создании добавляем CD/DVD 2 --- образ `Ubuntu`, CD/DVD 3 --- образ `Clonezilla`. После того, как закончим установку, эти образы можно будет убрать.

3) Теперь нужно найти `.vmx` файл виртуалки --- там записаны её конфиги в формате `ключ = значение`: `VM` -> `Settings` -> `Options`. Посмотреть Working directory (можно скопировать/вставить в проводник).

4) Удаляем строку:

    ```text
    usb.restrictions.defaultAllow = "FALSE"
    ```

5) Добавляем строки (в любом месте, можно после первой строки):

    ```text
    bios.bootdelay = 3000
    firmware = "efi"
    ```

6) Теперь запускаем виртуалку и нажимаем `esc`, чтобы попасть в `boot menu` (также можно нажать `F2` --- режим `enter setup`). На самом деле есть способ загрузиться в него сразу из GUI: [askubuntu.com](https://askubuntu.com/questions/343093/how-to-make-vm-boot-from-live-cd), но далее всё равно придётся использовать `esc`.

7) Подключаем съёмный диск к хосту (только в этот момент, иначе его не увидит виртуалка) и перезагружаем VM комбинацией клавиш `ctrl+alt+insert`

8) Теперь опять входим в `boot menu` и загружаемся с CD/DVD 3 --- образа `Clonezilla`. На самом деле перед нами будут строки вида `EFI VMware Virtual SATA CDROM Drive (X.Y)`. Нужные номера `X` и `Y` можно посмотреть в `.vmx` файле (но они могут быть записаны в обратном порядке).

9) После восстановления раздела через `Clonezilla` нужно изменить `/etc/fstab` и установить `grub`. Про то, как это делается, можно посмотреть в главе **Копия Ubuntu с помощью clonezilla**. Нужно только при старте VM зайти в `boot menu` и выбрать загрузку с CD/DVD 2 --- обычного образа `Ubuntu`.

### dd + rsync

Если делать через `dd`, то нужно самому создавать разделы и потом ещё boot the raw image as a hard disk or convert it to a virtualbox (or other) image: `VBoxManage convertdd ubuntu.img ubuntu.vdi`

Опорный гайд: [wiki.archlinux.org](https://wiki.archlinux.org/title/Moving_an_existing_install_into_(or_out_of)_a_virtual_machine).

Обсуждение: [www.reddit.com](https://www.reddit.com/r/linux4noobs/comments/jfz2b1/cloning_my_system_for_use_on_a_virtual_machine/).

1) Загружаемся с Ubuntu Live CD
2) Монтируем образ системы:

```bash
sudo mount --mkdir /dev/nvme0n1p<system_num> /mnt/System
sudo mnt --mkdir /dev/nvme0n1p<storage_num> /mnt/Storage
```

- Create partition. `fdisk` isn't good at working with GPT tables, so use `parted`.

```bash
sudo parted /mnt/Storage/VMImage/backup.img
```

TODO...

### Проблемы после переноса на виртуалку

1) `Фризы и заисания, чёрный экран` --- отключить `3d acceleration` в настройках VMWare, выполнить строки

    ```bash
    sudo add-apt-repository ppa:oibaf/graphics-drivers
    sudo apt-get update && sudo apt-get upgrade
    ```

    и после этого обратно включить `3d acceleration`.

2) `На виртуалке обрезанное окно`. Решение: [askubuntu.com](https://askubuntu.com/questions/768666/ubuntu-desktop-wont-go-full-screen-under-vm).

    По сути не был установлен пакет:

    ```bash
    sudo apt-get install open-vm-tools
    ```

3) `Oh no! Something went wrong.` --- ctrl+alt+F3 и выполнить:

```bash
sudo apt-get update && sudo apt-get dist-upgrade
sudo apt-get clean && sudo apt-get autoremove && sudo reboot
```

Ссылка на [решение](<https://askubuntu.com/questions/1239025/after-upgrade-to-ubuntu-20-04-oh-no-something-went-wrong>]


## Создание бекапов (резервное копирование)

- Windows: ...
- Ubuntu:
  - **Возможные варианты**: [askubuntu.com](https://askubuntu.com/questions/7809/how-to-back-up-my-entire-system), сравнение инструментов: [askubuntu.com](https://askubuntu.com/questions/2596/comparison-of-backup-tools).
  - **Ручное копирование с помощью `tar`**: [ubuntuforums.org](https://ubuntuforums.org/showthread.php?t=35087)
  - **deja dup**: только обычные файлы
  - **rsync**: [askubuntu.com](https://askubuntu.com/a/2634)
  - **TimeShift**: [askubuntu.com](https://askubuntu.com/a/1376066/1900253), [instruction: www.linuxtechi.com](https://www.linuxtechi.com/timeshift-backup-restore-ubuntu-linux/)
  
Бекап `Ubuntu` **ОБЯЗАТЕЛЬНО** делать на `ext4`. Большинство встроенных утилит работает только с этой ФС. Если нужно сделать бекап на `NTFS`, то для этого нужно использовать `tar` (иначе не будут сохраняться права на файл, его владельцы и прочее). Поэтому сделаем отдельный раздел с `ext4`, на который будем переносить данные.

На всякий случай, опишем резервное копирование с помощью `tar`:

TODO...

### Автоматические бекапы `Ubuntu`

Использовать `BackupMe`.

## Удаление `Ubuntu` с сохранением `Windows`

Полный гайд: [youtube](https://www.youtube.com/watch?v=2dB49eJkyCU).

- Для начала нужно восстановить загрузчик: `Windows Boot Manager`.
  1) Нужно зайти в `средство восстановление системы Windows`. Для этого можно взять флешку с образом и вместо `Установить` выбрать `Восстановление системы`. Или можно попробовать 3 раза перезагрузить комп во время его загрузки и надеяться, что нас перекинет в этот режим.
  2) Выбираем `Поиск и устранение неисправностей` -> `Дополнительные параметры` (может отсутствовать) -> командная строка. Вместо командной строки можно выбрать `Восстановление`.
  3) `bootrec.exe` --- посмотреть доступные опции. Для старых систем нужно выбрать `/FixMbr`, для новых --- `/FixBoot`. Если не уверены, какую команду использовать, можно прописать обе.
  4) `bootrec.exe /FixBoot`, затем закрываем и перезагружаем компьютер.

- Если описанное выше не помогло, то нужно переустановить загрузчик полностью. Опять же заходим в `средство восстановления системы Windows` в командную строку и пишем:

```text
diskpart
list vol # list of partitions

# Clean data of Boot partition
select vol 3
format fs=fat32
exit

# Create new Boot loader
bcdboot D:\Windows
### Must write `Файлы загрузки успешно созданы`
```

- Удаление `grub` и восстановление загрузчика прямо из командной строки без средства восстановления Windows: [youtube](https://www.youtube.com/watch?v=TPHUpu5ZD7c)

- После восстановления загрузчика осталось удалить раздел с Ubuntu: Win+R -> diskmgmt.msc ...


## Переезд на новый ПК

- Для винды устанавливаем всё из дистрибутива (или найти программу для резервного копирования).
- Для Ubuntu можно использовать Clonezilla. Порядок действий:
  - **Важно**: клонируется полностью раздел. Поэтому на разделе, куда будет копироваться информация, должно быть столько же или больше свободного места.


## Полезное

- Разделы и файловые системы linux: [ubuntu.ru](https://help.ubuntu.ru/wiki/%D1%80%D0%B0%D0%B7%D0%B4%D0%B5%D0%BB%D1%8B_%D0%B8_%D1%84%D0%B0%D0%B9%D0%BB%D0%BE%D0%B2%D1%8B%D0%B5_%D1%81%D0%B8%D1%81%D1%82%D0%B5%D0%BC%D1%8B_linux).

- Открыть управление дисками `Windows`: `Win+R` -> `diskmgmt.msc`.

- Разница `MBR` и `GPT` (второй более современный): [recoverit.wondershare.com](https://recoverit.wondershare.com/partition-tips/what-is-mbr.html)

- Хранить SSD отдельно нельзя --- при отсутствии питания на нём теряются данные.

- Grub and Windows boot manager must be installed into **the same EFI boot partition**: [askubuntu.com](https://askubuntu.com/questions/1506438/does-installing-grub-and-windows-boot-manager-on-the-same-efi-partition-cause-an).

- Either operating system may make itself first in the boot order after an update. This is easily fixed by changing bios settings or using the bios's EFI boot menu to select the desired OS (and possibly using OS tools to reset the boot order).

- Полезные команды:

    ```bash
    sudo fdisk -l   # partitions info
    sudo df -hT     # check all mounted devices
    sudo lsblk      # partitions info
    sudo blkid      # partition UUID
    ```

## TODO

- Как настроить `swap раздел`?
