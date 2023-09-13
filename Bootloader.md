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

- Select ```Arch Linux install medium (x86_64, UEFI)```

**Wifi configurations**
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
**Paritioning

