# Arch Linux Install Guide

Set your keyboard layout:
```
loadkeys br-abnt2
```

If the fonts are small:
```
setfont ter-132b
```

Check connection:
```
ping -c 3 google.com
```

Connect with wifi-menu:
```
wifi-menu
```

Or iwctl:
```
iwctl

# List the devices:
device list

# Replace wlan0 by your device:
station wlan0 scan

# No meu caso disse que não havia station para o wlan0. Eu tive que rodar o comando abaixo (fonte: https://unix.stackexchange.com/questions/632443/wifi-device-not-showing-up-while-setting-up-arch-linux):
# phy0 is the adapter from "device list" command

adapter phy0 set-property Powered on

# Show networks available:
station wlan0 get-networks

# Network connection:
station wlan0 connect nome_da_rede
```

Check the date:
```
timedatectl
```

Show block devices:
```
lsblk or fdisk -l
```

Partition the disks:
```
cfdisk /dev/sda
```

Only format the EFI system partition if you created it during the partitioning step. If there already was an EFI system partition on disk beforehand, reformatting it can destroy the boot loaders of other installed operating systems.

Example:

```
/mnt/boot or mnt/efi 	/dev/sda1 	EFI System Partition 	  260MB - 512MB               fat32
/mnt                  /dev/sda2 	Linux x86-64 root (/) 	Remainder of the device 	  ext4
SWAP                  /dev/sda3 	Linux swap 	            1G 	                        ext4
```

Format the partitions:
```
mkfs.fat -F32 /dev/sda1
mkfs.ext4 /dev/sda2
mkswap /dev/sda3
swapon /dev/sda3
```

Create the efi directory:
```
mkdir /mnt/efi
```

Mount the partitions:
```
mount /dev/sda2 /mnt
mount /dev/sda1 /mnt/efi
```

Enable parallel downloads on Pacman:
```
vim /etc/pacman.conf
```

Uncomment the line:
```
vim /etc/pacman.conf
```

Use reflector to select the best mirror:
```
reflector -c Brazil --save /mnt/etc/pacman.d/mirrorlist
```

Install the base packages:
```
pacstrap /mnt base base-devel linux-zen linux-firmware vim nano dhcpcd curl wget inetutils iwd zip unzip
```

Generate fstab:
```
genfstab -U -p /mnt >> /mnt/etc/fstab
```

Check if fstab is ok:
```
cat /mnt/etc/fstab
```

Change to the device:
```
arch-chroot /mnt
```

Use reflector to select the best mirror:
```
reflector -c Brazil --save /mnt/etc/pacman.d/mirrorlist
```

Set time zone:
```
ln -sf /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime
```

Run hwclock to generate /etc/adjtime:
```
hwclock --systohc 
```

Check the date:
```
date
```

Set the keyboard layout:
```
echo KEYMAP=br-abnt2 >> /etc/vconsole.conf  
```

Set root password:
```
passwd 
```

Create user:
```
useradd -m -g users -G wheel,storage,power -s /bin/bash andre
```

Set user password:
```
passwd andre
```

Install important packages to use the system:
```
pacman -S dosfstools os-prober mtools network-manager-applet networkmanager wpa_supplicant wireless_tools dialog reflector
```

Set the boot loader:
```
pacman -S grub efibootmgr
```
Install Grub:
```
grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=arch_grub --recheck
```

Create Grub config:
```
grub-mkconfig -o /boot/grub/grub.cfg
```

Adicionar o usuário ao sudo:
```
visudo
```

Uncomment the following line:
```
“%wheel ALL=(ALL:ALL) ALL”
```

Restart the system:
```
exit
reboot
```

Enable iwd:
```
systemctl enable --now iwd.service
iwctl
```

Check connection.
```
ping -c 3 www.google.com.br
```

Run the following command
```
sudo dhcpcd
```

Graphics:
```
sudo pacman -S xorg-server xorg-xinit xorg-apps mesa
```

Intel:
```
sudo pacman -S xf86-video-intel
```

Virtual machine:
```
sudo pacman -S xf86-video-vesa (J)
sudo pacman -S virtualbox-guest-utils (R)
```

