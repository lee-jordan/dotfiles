Arch notes

Keyboard Layout:
$ loadkeys uk

Verify boot mode:
$ ls /sys/firmware/efi/efivars


Update system clock:
$ timedatectl set-ntp true

partition:
$ lsblk
$ cfdisk /dev/sda

 - label type gpt
 - delete all current partitions
 - new 550mb partition of type EFI System
 - new partition with remainder
 - write
 - quit

Formatting the partitions:
$ mkfs.fat -F32 /dev/sda1 (efi)
$ mkfs.ext4 /dev/sda2

mounting:
$ mount /dev/sda2 /mnt
$ mkdir /mnt/boot
$ mkdir /mnt/boot/efi
$ mount /dev/sda1 /mnt/boot/efi

Installing essential packages:
$ pacstrap /mnt base linux linux-firmware base-devel

Fstab:
$ genfstab -U /mnt >> /mnt/etc/fstab

Chroot:
$ arch-chroot /mnt

Timezone:
$ ln -sf /usr/share/zoneinfo/Europe/London /etc/localtime
$ hwclock --systohc

Locale:
$ pacman -S nano

$ nano /etc/locale.gen
remove # at start of line
```en_Gb.UTF-8 UTF-8 line```
save and exit

$ locale-gen
$ nano /etc/locale.conf
```LANG=en_GB.UTF-8```
save and exit

$ nano /etc/vconsole.conf
```KEYMAP=uk```
save and exit


Hostname:
hostnameVariable here can be replaced with any word/name

$ echo hostnameVariable > /etc/hostname
$ nano /etc/hosts


```127.0.0.1   localhost
::1         localhost
127.0.0.1   hostnameVariable.localdomain    hostnameVariable```


NetworkManager:
$ pacman -S networkmanager
$ systemctl enable NetworkManager

Password:
$ passwd
Type hunter2 twice

Boot loader:
$ pacman -S grub efibootmgr
$ grub-install --target=x86_64-efi --efi-directory=/boot/efi
$ grub-mkconfig -o /boot/grub/grub.cfg

Reboot:
$ exit
$ umount -R /mnt
$ reboot

Pacman config:
$ nano /etc/pacman.conf
uncomment the following
 - Color
 - TotalDownload
 - [multilib]
 - Include = /etc/pacman.d/mirrorlist

 $ sudo pacman -Sy

Creating a user account:
userVariable here can be replaced with any name
$ useradd -mg users -G wheel,storage,power -s /bin/bash userVariable
$ passwd userVariable
$ EDITOR=nano visudo
uncomment the line ```#%wheel ALL=(ALL) ALL```
add to bottom of file ```Defaults passwd_timeout=0```
save ane exit
$ exit
login using username and password as above

Installing xorg:
$ sudo pacman -S xorg-server xorg-apps xorg-xinit xterm

Installing graphics drivers:
Nvidia
To figure out the graphics card
$ lspci -k | grep -A 2 -E "(VGA|3D)"
$ sudo pacman -S nvidia nvidia-libgl
$ reboot
$ sudo pacman -S lib32-nvidia-utils lib32-nvidia-libgl lib32-mesa-demos libva-vdpau-driver
$ sudo systemctl enable nvidia-persistenced.service
$ reboot

Check it worked
$ glxinfo32 | grep OpenGL
$ vainfo

AMD
$ sudo pacman -S mesa lib32-mesa xf86-video-amdgpu vulkan-radeon lib32-vulkan-radeon libva-mesa-driver lib32-libva-mesa-driver mesa-vdpau lib32-mesa-vdpau
$ reboot

Intel
get a new grahpics card

Desktop Manager:
$ sudo pacman -S sddm
$ sudo systemctl enable sddm.service


Window Manager:
$ sudo pacman -S plasma-meta
$ sudo pacman 
$ reboot

Yay/git:
$ sudo pacman -S git
$ git clone https://aur.archlinux.org/yay.git
$ cd yay
$ makepkg -si
$ cd ..
$ rm -rf yay

Zsh:
$ sudo pacman -S zsh
$ sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
$ git clone https://github.com/dracula/zsh.git ~/.userVariable/oh-my-zsh/themes
$ ln -s ~/.userVariable/oh-my-zsh/themes/dracula.zsh-theme ~/.oh-my-zsh/themes/dracula.zsh-theme

Package manager:
yes shut up
pamac-aur

file manager:
thunar
plugins?

software:
brave-bin remmina slack-desktop postman-bin code discord zoom solaar 
ckb-next neofetch audacity dbeaver baobab flameshot
gimp inkscape ksysguard lutris obs-studio qbittorrent
thunderbird vlc android-studio android-tools atlauncher-bin
bitwarden-bin dart deno-bin ffmpeg firefox mongodb-compass
npm spotify-adblock-linux steam intellij-idea-community-edition ark
galculator convertall gcolor2 portainer-bin docker docker-compose
qview thunderbird albert muparser konsole megasync leafpad popcorntime-bin
qemu libvirt virt-manager maven ttf-fira-code ttf-jetbrains-mono 
ttf-roboto-mono ttf-oxygen-gf peek corectrl




Fonts:
$ yay -S freetype2-cleartype
If it asks to replace freetype2 say yes.

JetBrains:
plugins for this are Dracula Theme and VS Code Keymap. 
Be sure to change the font to the same as system too.

VScode:
Plugins for this are Dracula Official, Dart, Flutter, GitLens, Material Icon Theme, Prettier
Change font to same as system and enable font ligatures.

Docker:
Assuming docker has been installed via yay already
$ systemctl start docker.service
$ systemctl enable docker.service
$ sudo group add docker
$ sudo usermod -aG docker $USER
Portainer:
This may be better to do inside a swarm cluster but idk what that is. Investigation needed.
$ docker volume create portainer_data
$ docker run -d -p 9000:9000 -p 8000:8000 --name portainer --restart always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer

Brave:
Bitwarden
ImprovedTube/Improve Youtube/Iridium
JSONView
Momentum
Picture in Picture for every website
Tampermonkey
    - https://draculatheme.com/duckduckgo
WhatRuns
The Great Suspender

Slack:
https://draculatheme.com/slack

Steam:
cd ~/.local/share/Steam/skins/ && git clone https://github.com/dracula/steam.git 'Dracula'
open Steam and go to Steam -> Settings -> Preferences and open the section Interface



Global Theme: Dracula
Plasma Style: Breeze
Application Style: Breeze
Window Decorations: Dracula
Colours: Dracula
Fonts: Fira Code Retina
icon: Papirus-Dark
Cursors: Breeze
Desktop Background: Set each image to respective monintors. Background color of #0c0c2b with stretching if required.
Stupid bounce load thing: System Settings > Personalization - Applications > Launch Feedback

konsole config ~/.local/share/konsole

Google drive:
$ yay -S google-drive-ocamlfuse
$ mkdir ~/google-drive
// This may need to go in the startup file thing
$ google-drive-ocamlfuse /google-drive

