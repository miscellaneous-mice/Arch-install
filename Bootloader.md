# Arch install on physical hardware

## Preconfigurations

**Installations**
- Install the arch iso file from here : https://archlinux.org/download/
- Install the balena etcher from here : https://etcher.balena.io/

**Secure boot settings** (For lenovo legion)
- Hit power button, press F2 and see if you load into the boot menu
- More settings -> Security -> Secure boot -> Disable
- Press F10 to save and exit -> Save config changes (YES)

**Loading iso file into the flash drive and boot from it**
- Link the iso file into the flash drive using balena etcher
- Plug the flash drive into the PC before booting
- Hit power and hit the bootloader key (For lenovo legion its : F12 or Fn+F12)
- Select the boot device from the menu (Press enter)

## Initial configurations

- **Select** ```Arch Linux install medium (x86_64, UEFI)```

### Wifi configurations
- To get an interactive prompt do:
```
$ iwctl
```
- First, if you do not know your wireless device name, list all Wi-Fi devices:
```
[iwd]# device list
```
- If the device or its corresponding adapter is turned off, turn it on:
```
[iwd]# device device set-property Powered on
[iwd]# adapter adapter set-property Powered on
```
- Then, to initiate a scan for networks (note that this command will not output anything):
```
[iwd]# station device scan
```
- You can then list all available networks:
```
[iwd]# station device get-networks
```
- Finally, to connect to a network:
```
[iwd]# station device connect SSID
```
- Exit the prompt
```
[iwd]# exit
```
- Check the internet connection
```
$ ping www.google.com
```

### System check
- Checking if we've booted into efi mode
```
$ efivar -l
```

## Disk configuration

### Getting the info
- List all the storage devices and check which drive you wanna install linux to (in my case ssd)
```
$ lsblk
```
- List checking which disk is ssd (samsung) or hdd (toshiba) etc.
```
$ lsblk -do +VENDOR,MODEL,SERIAL
```

### Partitioning
- After knowing which drive is your drive do this (in my case sda)
```
$ gdisk /dev/sda
```
- gdisk options
  - Command -> ```x```
  - Expert command -> ```z```
  - About to wipe the GPT -> ```y```
  - Blank out MBR? -> ```y```

- Now use cgdisk (partitioning tool)
```
$ cgdisk /dev/sda
```

**Now its all paritioning no commands**

- Step to make partition
  - Press New
  - First sector : type in the field, else leave it blank (default)
  - Size : ___ MiB, GiB
  - Hex code : Hex code to specify our File system type (boot:EF00)
  - Partition name : boot / swap / root / home

- For boot paritition (sda1)
  - First sector : ENTER
  - Size : 1024MiB
  - Hex Code : EF00
  - Partition name : boot

- For swap partition (sda2)
  - First sector : ENTER
  - Size : 16GiB
  - Hex Code : 8200
  - Partition name : swap 


- For root partition (sda3)
  - First sector : ENTER
  - Size : 40GiB
  - Hex Code : ENTER
  - Partition name : root  
  

- For home paritition (sda4)
  - First sector : ENTER
  - Size : ENTER
  - Hex Code : ENTER
  - Partition name : home

- Now we need to save this configuration so
  - Press Write
  - Are you sure you want to write the partition table to disk? : yes
  - Operation completed : ENTER
  - quit

### Formatting disks

**Specifying the filesystem types**

- Boot should be FAT32
```
$ mkfs.fat -F32 /dev/sda1
```
- Swap configuration
```
$ mkswap /dev/sda2
$ swapon /dev/sda2
```
- Last 2 partitions (root, home) are in ext4 format
```
$ mkfs.ext4 /dev/sda3 (If says Proceed anyways : y)
$ mkfs.ext4 /dev/sda4 (If says Proceed anyways : y)
```
- To check on the partitions done run
```
$ lsblk
```

### Making mounts, pointing it.

- For installation of arch key components, we need to mount the following filesystem
```
$ mount /dev/sda3 /mnt
$ mkdir /mnt/boot
$ mkdir /mnt/home
$ mount /dev/sda1 /mnt/boot
$ mount /dev/sda4 /mnt/home
```

## Kernel configurations and installation

### Pacman configuration (optional)

- Updating our mirrorlist
```
$ cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.bak
```
- Ranking mirrors
```
$ sudo pacman -Sy pacman-contrib
$ rankmirrors -n 6 /etc/pacman.d/mirrorlist.backup > /etc/pacman.d/mirrorlist
```
- Verifying our ranked mirrors (optional)
```
$ nano /etc/pacman.d/mirrorlist -> Do nothing
```

### Linux kernels

- Installing linux kernels
```
$ pacstrap -K /mnt base linux linux-firmware base-devel
```

### Creating fstab for recognising hardrives for allocating, booting into our drive.

- Creating fstab
```
$ genfstab -U -p /mnt >> /mnt/etc/fstab
```
- Verifying fstab (optional)
```
$ nano /mnt/etc/fstab -> Do nothing
```

## Basic Configurations (locale, time, keyboard, language, etc.)

- Booting into our installation underneath the drive
```
$ arch-chroot /mnt
```
- Install vim and bash autocomplete
```
$ sudo pacman -S vim bash-completion
```
- Enabling our locales
```
$ vim /etc/locale.gen -> Uncomment -> en_US.UTF-8 UTF-8
```
- Verifying our locale-gen
```
$ locale-gen
```
- Echo and create the language
```
$ echo LANG=en_US.UTF-8 > /etc/locale.conf
$ export LANG=en_US.UTF-8
```
- Configuring timezone
```
$ ln -s /usr/share/zoneinfo/Asia/Kolkata > /etc/localtime
```
- Sync our hardware clock
```
$ hwclock --systohc --utc
```
- Create an hostname for our system
```
$ echo ArchBox > /etc/hostname
```
- Enabling trim support for our ssd
```
$ systemctl enable fstrim.timer
```
- Enabling 32-bit support
```
$ vim /etc/pacman.conf -> Uncomment -> [multilib]
                                       Include = /etc/pacman.d/mirrorlist
$ sudo pacman -Sy
```
- Setting our root password
```
$ passwd
```
- Adding our user
```
$ useradd -m -g users -G wheel,storage,power -s /bin/bash megame
```
- Adding password to our user
```
$ passwd megame
```
- Modifying the sudo file
```
$ EDITOR=vim visudo -> Uncomment -> %wheel ALL=ALL(ALL:ALL) ALL
                    -> At the end add : Defaults rootpw
```


## Installing the bootloader

- Verifying if we are in a efi system
```
$ mount -t efivarfs efivarfs /sys/firmware/efi/efivars/
$ ls /sys/firmware/efi/efivars/
```

- Install the bootloader
```
$ bootctl install
```
- Writing the boot entries
```
$ vim /boot/loader/entries/arch.conf -> Type the boot entry given below
-----------------------------
title Arch
linux /vmlinuz-linux
initrd /initramfs-linux.img
-----------------------------
```
- We need to create line options and link to our root drive where our system is stored
```
$ echo "options root=PARTUUID=$(blkid -s PARTUUID -o value /dev/sda3) rw" >> /boot/loader/entries/arch.conf
$ vim /boot/loader/entries/arch.conf
```

## Hardware Configuration

### Configuring our intel CPU

- Installing intel-ucode






