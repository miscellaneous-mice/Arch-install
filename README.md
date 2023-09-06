# Arch-install

## Basic configuration

```
  timedatectl set-ntp true
```
- *To paritition Disks*

```
  cfdisk /dev/sda -> select dos (for virtual machine)
```
  - This is how you will partition
```
  sda1 -> 1024M/1G -> boot disk -> select and press B (for making the disk bootable)
  sda2 -> 30G -> home and other stuff
  *optional -> sda3 -> 1G (make sure sda2 is made as 29G) -> Extra buffer ram

  write and exit out of the cfdisk configuration menu
```
  - Specifying the type of disk (ext4, fat32, etc..)
```

```
