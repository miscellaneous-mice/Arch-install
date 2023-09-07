# Arch-install

  - iso download - > https://archlinux.org/download/

## Virtual box configuration

  - Hardware specs
```
  RAM -> 8192 MB
  Processor -> 4 CPU's
  Video Memory -> 128 MB
  Storage -> 30 GB
```
  - Other settings
```
  System -> boot order -> select Hard Disk as top
  Display -> Enable 3D accelaration
  Storage -> Select your arch iso file
```

## Basic configuration

```
  Other stuff like wifi configuration etc. (see arch wiki or denshi)
  loadkeys us
  timedatectl set-ntp true
```
### To paritition Disks

```
  For checking the disks use -> lsblk
  cfdisk /dev/sda -> select dos (for virtual machine)
```
  - This is how you will partition
```
  sda1 -> 1024M/1G -> boot disk -> select and press B (for making the disk bootable)
  sda2 -> 30G -> home and other stuff
  *optional -> sda3 -> 1G (make sure sda2 is made as 29G) -> Extra buffer ram (swap memory)

  write and exit out of the cfdisk configuration menu
```
  - Specifying the type of disk (ext4, fat32, etc..)
```
  mkfs.ext4 /dev/sda1
  mkfs.ext4 /dev/sda2
  *optional -> if you've created swap -> mkswap /dev/sda3
```
  - Mounting the disks created to boot and mnt
```
  mount /dev/sda2 /mnt
  mkdir /mnt/boot
  mount /dev/sda1 /mnt/boot
  *optional -> if you've created swap -> swapon /dev/sda3
```

## Installing Basic linux kernels and configurations

  - Installing Linux kernels and development tools
```
  pacstrap /mnt base base-devel linux linux-firmware vim
```
  - Fstab configuration (files that linux tries to load in when booting)
```
  genfstab -U /mnt >> /mnt/etc/fstab
```
  - Chroot into arch installation i.e. changing root from our USB to our actual Arch linux installation
```
  arch-chroot /mnt /bin/bash
```
  - Couple of crucial packages (Initializing our network adapter and bootloader at boot time)
```
  pacman -S networkmanager grub
  systemctl enable NetworkManager
  grub-install /dev/sda
  grub-mkconfig -o /boot/grub/grub.cfg
```

## Final Process

  - Setting our root password
```
  passwd
```
  - Setting our language (in this case english us)
```
  vim /etc/locale.gen -> uncomment both lines containing en-US
  locale-gen -> to see if our languages are configured properly
  vim /etc/locale.conf -> type -> LANG=en_US.UTF-8
  vim /etc/hostname -> type -> Archbox
```
  - Setting our region
```
  ln -sf /usr/share/zoneinfo/Asia/Kolkata /etc/localtime
```
  - Now to reboot our system
```
  exit
  umount -R /mnt
  reboot
```

## Post-installation steps

  - Verifying if our system is proper
```
  pacman -S neofetch
  neofetch
```
  - Adding user to our system
```
  useradd -mg wheel megame 
  passwd megame
  vim /etc/sudoers -> uncomment -> %wheel ALL = (ALL)ALL
  reboot
```

### Different Distro's for linux

  - If you wanna install KDE
```
  sudo pacman -S xorg xorg-server
  sudo pacman -S plasma sddm
  sudo pacman -S konsole kate firefox
  sudo systemctl start sddm
  sudo systemctl enable sddm
```
  - If you wanna install gnome
```
  sudo pacman -S xorg xorg-server
  sudo pacman -S gnome lxdm
  sudo pacman -S konsole kate firefox
  sudo systemctl start lxdm.service
  sudo systemctl enable lxdm.service
```
  - If you wanna install xfce
```
  sudo pacman -S xorg xorg-server xfce4 xfce4-goodies lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings
  sudo systemctl start lightdm
  sudo systemctl enable lightdm
```







    
