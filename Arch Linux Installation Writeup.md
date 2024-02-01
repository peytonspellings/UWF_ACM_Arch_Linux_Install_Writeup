UWF ACM
Peyton Spellings
# Sources

1. https://wiki.archlinux.org/title/installation_guide
2. https://www.youtube.com/watch?v=FudOL0-B9Hs&list=PL-odKaUzOz3KncXrksLeh386gUSgrCbbe&index=4&t=846s&ab_channel=EF-LinuxMadeSimple
3. https://www.youtube.com/watch?v=_JYIAaLrwcY&ab_channel=SomeOrdinaryGamers

# Required Resources

## Hardware
1. Computer
2. Computer you want to install Arch Linux on (They can be the same)
3. USB Flash Drive
4. Internet
## Software
1. Balena Etcher (or similar program) https://etcher.balena.io/

# Initial Setup

1. Download the Arch Linux ISO file from the official source. There are torrent magnet links and HTTP mirrors available. https://archlinux.org/download/
2. Download and Install Balena Etcher. https://etcher.balena.io/
3. Plug in your USB Flash Drive and open Balena Etcher.
4. Choose `Flash from file` and select your downloaded Arch Linux ISO
5. Choose `Select target` and select your USB flash drive (Do not choose the drive your OS is install on)'
6. Press `Flash!`
7. Wait for the flash to finish

# Launching the Installation Media

1. Plug the USB Flash Drive into the computer you want to install Arch Linux on
2. Turn the computer OFF
3. Turn the computer on and press and spam `F12` on the keyboard until you reach the boot selection menu. This step can vary for different hardware.
4. Select your USB Flash Drive from the list of drives. This is the drive you are booting from.
5. Wait until you have a working command line interface (CLI)

# OS Installation

## Internet Config

### Ethernet

Works out of the box :D :D :D

### WIFI

https://wiki.archlinux.org/title/Iwd#iwctl

1. `iwctl`
2. `device list`
3. `station wlan0 scan`
4. `station wlan0 get-networks`
5. `station wlan0 connect WIFI_SSID`
6. `exit`

## Drive Partitioning 

### BIOS/MBR

https://www.youtube.com/playlist?list=PL-odKaUzOz3KncXrksLeh386gUSgrCbbe

1. `lsblk`
2. `gdisk /dev/sda`
	1. `x` Expert command
	2. `z` Zap (Wiping the Drive)
	3. `y`
	4. `y`
4. `cfdisk /dev/sda`
5. Choose `dos`
6. Swap
	1. New
	2. `4GB`
	3. Primary
	4. Type
		1. 82 Linux Swap
7. Root
	1. New
	2. Default
	3. Primary
8. Write
	1. `yes`
9. Quit
10. `mkfs.ext4 /dev/sda2`
11. `mkswap /dev/sda1`
12. `swapon /dev/sda1`
13. `mount /dev/sda2 /mnt`
14. `lsblk`

## Update the Mirror List

1. `cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.backup`
2. `pacman -Sy pacman-contrib`
3. `rankmirrors -n 6 /etc/pacman.d/mirrorlist.backup > /etc/pacman.d/mirrorlist`

## Installing the Linux Kernel

1. `pacstrap -K /mnt base linux linux-firmware nano`

## fstab Configs

1. `genfstab -U -p /mnt >> /mnt/etc/fstab`
2. `nano /mnt/etc/fstab`

## More Arch Config

1. Don't reboot
2. `arch-chroot /mnt`
3. `pacman -S nano bash-completion`
4. Language Config
	1. `nano /etc/locale.gen`
	2. CTR + W
	3. `en_US.UTF-8`
	4. CTR + X to save
	5. `locale-gen`
	6. `echo LANG=en_US.UTF-8 > /etc/locale.conf`
	7. `export LANG=en_US.UTF-8`
5. Time zones
	1. `ls /usr/share/zoneinfo`
	2. `ln -s /usr/share/zoneinfo/US/Central > /etc/localtime`
	3. `hwclock --systohc --utc`
6. Hostname
	1. `echo uwf_acm > /etc/hostname`
7. 32 bit support
	1. `nano /etc/pacman.conf`
	2. CTR + W `multilib`
	3. Remove both `#` signs
	   ```
	   [multilib]
	   Include = /etc/pacman.d/mirrorlist
       ```

	4. CTR + X to save
	5. `pacman -Sy`
8. Root Password
	1. `passwd`
	2. Enter your password here (DONT FORGET IT)
	3. `useradd -m -g users -G wheel,storage,power -s /bin/bash acm`
	4. `passwd acm`
	5. Enter a different password here (your main account)

## Installing the Bootloader BIOS/MBR

1.  `pacman -S grub networkmanager network-manager-applet dialog wireless_tools wpa_supplicant os-prober mtools dosfstools base-devel linux-headers`
2. `grub-install --target=i386-pc /dev/sda`
3. `grub-mkconfig -o /boot/grub/grub.cfg`
4. `exit`
5. `umount -a`
6. `reboot`

## Internet Config Again

1. `systemctl start NetworkManager`
2. Ethernet
	1. `ip a`
	2. Already Configured
3. WIFI
	1. `nmtui`
	2. Activate Connection
	3. Connect to your network
4. `systemctl enable NetworkManager`

## Sudo Permissions

1. `su root`
2. `EDITOR=nano visudo`
3. Uncomment this line
   ```
   %wheel ALL=(ALL:ALL) ALL
   ```

## Graphics Driver

1. Intel Graphics
	1. `pacman -S xf86-video-intel`
2. AMD
	1. `pacman -S xf86-video-amdgpu`
3. Nvidia
	1. `pacman -S nvidia nvidia-utils`

## Display Server

1. `pacman -S xorg`
2. Accept all the defaults

## Display Manager

1. `pacman -S lxdm`
2. Accept all the defaults
3. `systemctl enable lxdm.service`

## Desktop Manager 
The Fun part :D

1. `pacman -S xfce4 xfce4-goodies pulseaudio pavucontrol`
2. Accept all the defaults
3. `reboot

## Extra Stuff

1. `sudo pacman -Syyu`
2. `sudo pacman -S firefox`
