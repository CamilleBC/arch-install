# arch-install
The goal is to have a global install script in master, and maybe some specific scripts for different machines. Those may simply be in the repo, or in different branches, depending on what is more comfortable.
## Steps
### 1. Arch configuration
1. Mount partitions where they need to be (EFI + data)
2. Configure network access (wireless with wpa_supplicant or lan)
3. pacstrap base base-devel /mnt
4. Install grub:
`pacman -Sy grub`
5. Install intel microcode:
`pacman -Sy intel-ucode`
6. Install os-prober for Grub2:
`pacman -Sy os-prober`
7. Configure grub bootloader (using EFI if there is such a partition)
`grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=arch-grub`
8. Generate the grub config:
`grub-mkconfig -o /boot/grub/grub.cfg`
9. **IMPORTANT**: ***TODO:*** Install *openssh* and find a way to add a ssh key to github, using a script. Then pull the dotfiles.
### 2. Network configuration
* Content for the wpa_supplicant conf, if the protocol is WPA2/AES (located in /etc/wpa_supplicant/wpa_supplicant-***interface***.conf)
```
ctrl_interface=/run/wpa_supplicant                                                
ctrl_interface_group=0                                                            
update_config=1                                                                   
                                                                               
network={                                                                         
  ssid="SSID"                                                         
  proto=RSN                                                                     
  key_mgmt=WPA-PSK                                                              
  pairwise=CCMP                                                                 
  group=CCMP                                                                    
  psk="PASSWORD"                                   
}                                                                                                               
```
* start dhcpcd as a service as well:
`systemctl enable dhcpcd@INTERFACE.service`
* [wpa_supplicant as a systemd service](https://www.ianweatherhogg.com/tech/2015-08-05-creating-systemd-wpa_supplicant-instance.html):
`systemctl enable wpa_supplicant@INTERFACE.service`
### 3. AUR manager configuration
Managing AUR packages with [trizen](https://github.com/trizen/trizen).
Install trizen using the following commands:
```
git clone https://aur.archlinux.org/trizen.git
cd trizen
makepkg -si
```
### 4. Fish configuration
1. Install [fish](https://github.com/fish-shell/fish-shell):
`trizen -Sy fish`
2. Install [thefuck](https://github.com/nvbn/thefuck):
`trizen -Sy thefuck`
3. Install [fisherman](https://github.com/fisherman/fisherman):
`curl -Lo ~/.config/fish/functions/fisher.fish --create-dirs https://git.io/fisher`
4. Install the fisherman plugins:
`fisher fzf edc/bass omf/thefuck`
### 5. Termite configuration
1. Install [termite](https://github.com/thestinger/termite):
`trizen -Sy termite`
2. Install [onedark base16 color theme](https://github.com/khamer/base16-termite/blob/master/themes/base16-onedark.config):
```
mkdir -p ~/.config/termite
curl https://raw.githubusercontent.com/khamer/base16-termite/master/themes/base16-onedark.config >> ~/.config/termite/config
```
### 6. Xorg server configuration
### 7. I3 configuration
### 8. Dev Env configuration
#### 1. NVM configuration
To be able to use the Node.js Version Manager in fish shell, follow these steps:
1. Install NVM manually (https://github.com/creationix/nvm)
2. Install bass (https://github.com/edc/bass)
3. Add a `nvm.fish` file with the following function to fish/autoload
```
function nvm
   bass source ~/.nvm/nvm.sh --no-use ';' nvm $argv
end
```
4. Type the following command `nvm use default --silent`
5. Install last stable node.js:
`nvm install stable`
6. Set stable version to default
`nvm alias default stable`
#### 2. VScode configuration
