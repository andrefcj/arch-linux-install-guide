# arch-linux-install-guide
Arch Linux Install Guide

loadkeys br-abnt2

Se as letras estiverem pequenas:

setfont ter-132b

Habilitar os downloads paralelos no Pacman:

vim /etc/pacman.conf

Verificar conexão com a internet:

ping -c 3 google.com

WIFI:

O professor Juliano Ramos recomenda rodar o comando abaixo:

wifi-menu

Se estiver instalando em um notebook, para conectar via wi-fi:

Listar dispositivos:
iwctl

device list

Substitua wlan0 pelo seu dispositivo:
station wlan0 scan

No meu caso disse que não havia station para o wlan0. Eu tive que rodar o comando abaixo (fonte: https://unix.stackexchange.com/questions/632443/wifi-device-not-showing-up-while-setting-up-arch-linux):

adapter phy0 set-property Powered on

Onde phy0 é o Adapter mostrado no comando device list.

station wlan0 get-networks

station wlan0 connect nome_da_rede


Verificar se o relógio do sistema está certo:

timedatectl

Ver os dispostivos de bloco:

lsblk ou fdisk -l

Particionar:

cfdisk /dev/sda

Selecionar gpt

Se o disco do qual você deseja inicializar já tiver uma partição de sistema EFI, não crie outra, apenas use a partição existente.

mkfs.fat -F32 /dev/partição_de_sistema_efi (sda1)
mkfs.ext4 /dev/partição_raiz (sda2)
mkswap /dev/partição_swap (sda3)
swapon /dev/sda3

Sugestão de particionamento:

/mnt/boot or mnt/efi 	/dev/sda1 	EFI System Partition 	260MB - 512MB               fat32
/mnt                    /dev/sda2 	Linux x86-64 root (/) 	Remainder of the device 	ext4
[SWAP]                  /dev/sda3 	Linux swap 	            1G 	                        ext4

Montar a partição:

mount /dev/sda2 /mnt

Criar as pastas:

mkdir /mnt/efi (J)

mkdir /mnt/boot
mkdir /mnt/boot/efi

Montar as partições:

mount /dev/sda1 /mnt/efi (J)

mount /dev/sda2 /mnt (já foi montada acima)
mount /dev/sda1 /mnt/boot
mount /dev/sda1 /mnt/boot/efi
Se der erro no mount acima, rodar novamente os comandos abaixo:
mkdir /mnt/boot/efi
mount /dev/sda1 /mnt/boot/efi

Selecionar um espelho mais próximo:

vim /etc/pacman.d/mirrorlist

Ver se tem a ufscar e colocar na primeira posição.

Instalar o sistema:

pacstrap /mnt base base-devel linux-zen linux-firmware vim dhcpcd curl wget inetutils iwd zip unzip

inetutils tem o ifconfig
iwd tem o iwctl para conectar no wifi


Gerar o fstab:

genfstab -U -p /mnt >> /mnt/etc/fstab

Verificar se o fstab está correto:

cat /mnt/etc/fstab

arch-chroot /mnt

Habilitar os downloads paralelos no Pacman:

vim /etc/pacman.conf

Selecionar um espelho mais próximo:

vim /etc/pacman.d/mirrorlist

Configurar o fuso horário:

ln -sf /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime

Sincronizar o relógio com a BIOS:

hwclock --systohc 

Conferir a hora:

date

Configure o teclado:

echo KEYMAP=br-abnt2 >> /etc/vconsole.conf  

Senha para o usuário root:

passwd 

Criação do usuário:

useradd -m -g users -G wheel,storage,power -s /bin/bash andre

Definir a senha do novo usuário:

passwd andre

Instalar pacotes necessários para uso do sistema:

pacman -S dosfstools os-prober mtools network-manager-applet networkmanager wpa_supplicant wireless_tools dialog

Configuração do GRUB:

pacman -S grub efibootmgr

grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=arch_grub --recheck

Se não houver erros, rodar o comando:

grub-mkconfig -o /boot/grub/grub.cfg

Adicionar o usuário ao sudo:

visudo

Descomente a linha abaixo e salve:

“%wheel ALL=(ALL:ALL) ALL”

Reiniciar a máquina:

exit
reboot

Habilitar o iwd (para conectar pelo wifi):

systemctl enable --now iwd.service
iwctl

Verificar se existe conexão com a internet.

ping -c 3 www.google.com.br

Caso não exista, rodar os comandos para conectar com o wifi ou rodar o comando abaixo:

dhcpcd

Parte gráfica:

sudo pacman -S xorg-server xorg-xinit xorg-apps mesa

Se a placa de vídeo for Intel:
sudo pacman -S xf86-video-intel

Se for VM:

sudo pacman -S xf86-video-vesa (J)
sudo pacman -S virtualbox-guest-utils (R)
