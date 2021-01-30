# dotfiles

## Arch Bootable Media

### 1. Check internet status 

```sh
ping 1.1.1.1
```

안되면 다른거 설정

### 2. Partitioning

```sh
fdisk /dev/sd(a ~ x)
  도움말 m , 새 파티션 n, 파티션 보기 p, 파티션 삭제 d,
  n 에서는 용량을 -10GB 식으로 조정가능
  파티션 코드 19 : Linux swap
  3개 /, /boot, /swap 만들 것 (swap은 RAM 용량에 맞춰서)
```

```sh
mkfs.ext4 /dev/sd(x)(number) # number를 ext4 로 포맷
```

```sh
> mkswap /dev/sd(x)(number) # number를 스왑으로 만듬
```

### 3. Enable swap

```
swapon /dev/sd(x)(number)
```

### 4. Mount

```
mount /dev/sd(x)(number) /mnt
mkdir /mnt/boot
mount /dev/sdb(x)(number [boot]) /mnt/boot
```

### 5. Add Korean mirrorlist

```sh
vim /etc/pacman.d/mirrorlist
```

한 뒤에

```sh
Server = http://ftp.kaist.ac.kr/ArchLinux/$repo/os/$arch
Server = http://mirror.premi.st/archlinux/$repo/os/$arch
Server = http://ftp.harukasan.org/archlinux/$repo/os/$arch
```

추가

### 6. Add packages to local Arch

```sh
pacstrap /mnt linux linux-firmware base base-devel efibootmgr grub amd-ucode dhcpcd netctl zsh git vim neovim openssh xterm
```

base-devel: essential utils

efibootmgr: EFI boot manager

amd-ucode : Microcode for AMD processor (for Intel processor, use `intel-ucode`)

dhcpcd : DHCP client

netctl : Networking Utilities

zsh : Bash alternative

vim : Vi.

openssh: OpenSSH Server

xterm: Terminal


```sh
genfstab -U /mnt > /mnt/etc/fstab
```

## Local Arch

```sh
arch-chroot /mnt # 여기서부터 설치된 아치로 진입.
```

### 7. Locale Settings

```sh
echo LANG=en_US.UTF-8 > /etc/locale.conf
locale-gen
```

### 8. Set Hostname

```sh
echo (Your host name) > /etc/hostname
```

### 9. Set Timezone

```sh
ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime
pacman -S ntp
hwclock -w -l
```

### 10. Make EFI

```sh
mkdir /mnt/efi
mount /dev/sd(x)(number [efi]) /mnt/efi
grub-install --target=x86_64-efi --efi-directory=/mnt/efi --bootloader-id=GRUB
grub-mkconfig -o /boot/grub/grub.cfg
```

### 11. Set-up a user

```
groupadd autologin
useradd (유저 이름)
usermod -aG autologin (유저 이름)
sed -i 's/#pam-service=lightdm/pam-service=lightdm-autologin/g' /etc/lightdm/lightdm.conf
sed -i 's/#autologin-user=/autologin-user=(유저 이름)/g’ /etc/lightdm/lightdm.conf
sed -i 's/#autologin-session=/autologin-session=i3/g' /etc/lightdm/lightdm.conf
```

### 12. Install lightdm, xorg

```sh
pacman -S lightdm xorg xorg-xinit
sed -i 's/#logind-check-graphical=false/logind-check-graphical=true/g' /etc/lightdm/lightdm.conf
systemctl enable lightdm
```

### 13. Install i3-gaps-rounded

```sh
git clone https://aur.archlinux.org/i3-gaps-rounded-git.git
cd i3-gaps-rounded-git
makepkg -si
```

### 14. Install spacevim

```sh
curl -sLf https://spacevim.org/install.sh | zsh
```

### 15. Install Google Chrome

```sh
git clone https://aur.archlinux.org/google-chrome.git
cd google-chrome
makepkg -si
```

### 16. Etc

```sh
pacman -S alsa-utils pulseaudio-alsa pulseaudio-bluetooth bluez-utils
pacman -S picom 
pacman -S nemo
```

### 17. Clone this repository

```sh
git clone --bare https://github.com/So-chiru/dotfiles $HOME/.myconf
alias config='/usr/bin/git --git-dir=$HOME/.myconf --work-tree=$HOME'
config checkout
config config --local status.showUntrackedFiles no

# do your self
``` 
