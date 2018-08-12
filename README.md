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
`sudo systemctl enable dhcpcd@INTERFACE.service`
* [wpa_supplicant as a systemd service](https://www.ianweatherhogg.com/tech/2015-08-05-creating-systemd-wpa_supplicant-instance.html):
`sudo systemctl enable wpa_supplicant@INTERFACE.service`
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
`curl -Lo ~/.config/fish/functions/fisher.fish --create-dirs https://git.io/fisher;`
4. Install the fisherman plugins:
`fisher fzf edc/bass omf/thefuck;`
### 5.1 Simple Terminal configuration (option)
1. Download all necessary sources for [st](https://st.suckless.org):
```
curl -LJO https://dl.suckless.org/st/st-0.8.1.tar.gz
tar -xf st-0.8.1.tar.gz; cd st-0.8.1
curl -LJO https://st.suckless.org/patches/scrollback/st-scrollback-0.8.diff
curl -LJO https://st.suckless.org/patches/scrollback/st-scrollback-mouse-0.8.diff
curl -LJO https://st.suckless.org/patches/scrollback/st-scrollback-mouse-altscreen-0.8.diff
```
2. Download the config.h file:
`curl -LJ https://gist.githubusercontent.com/CamilleBC/065a62a10291a54f69a20900ffb2bef2/raw -o config.h`
3. Patch the scrollback in st sources:
```
patch -p1 < st-scrollback-0.8.diff
patch -p1 < st-scrollback-mouse-0.8.diff
patch -p1 < st-scrollback-mouse-altscreen-0.8.diff
```
4. Install st:
`sudo make install`
5. Clean up:
`cd ..; rm -rf st-0.8.1`
### 5.2 Termite configuration (Option 2)
1. Install [termite](https://github.com/thestinger/termite):
`trizen -Sy termite`
2. Install [onedark base16 color theme](https://github.com/khamer/base16-termite/blob/master/themes/base16-onedark.config):
```
mkdir -p ~/.config/termite
curl https://raw.githubusercontent.com/khamer/base16-termite/master/themes/base16-onedark.config >> ~/.config/termite/config
```
### 6. Xorg server configuration
### 7. I3 configuration
#### 1. Session management with abduco
1. Install [abduco](https://github.com/martanne/abduco)
```
curl -LJO https://github.com/martanne/abduco/archive/master.zip
unzip abduco-master.zip; cd abduco-master
./configure; make; sudo make install
cd .. ; rm -rf abduco-master; rm abduco-master.zip
```
#### 2. Rofi as a drop-in replacement for dmenu
```
trizen -Sy rofi
trizen -Sy rofi-dmenu
```
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
### 9. Package list
* [adobe-source-code-pro-fonts 2.030ro+1.050it-4](https://github.com/adobe-fonts/source-code-pro): programming font.
* [chromium 68.0.3440.106-1](): open-source WEB browser.
* [cpupower 4.16-1](https://github.com/torvalds/linux/tree/master/tools/power/cpupower): CPU frequency manager. WITH laptop-mode-tools.
* [efibootmgr 16-1](https://github.com/rhboot/efibootmgr): EFI boot manager. WITH grub2.
* [fish 2.7.1-1](https://github.com/fish-shell/fish-shell): user shell.
* [fzf 0.17.4-1](https://github.com/junegunn/fzf): Fuzzy finder. WITH neovim & fish.
* [git 2.18.0-1](https://git-scm.com/): Software version manager.
* [grub 2:2.02-7](https://www.gnu.org/software/grub/): Unix bootloader.
* [i3-gaps 4.15.0.1-2](https://github.com/Airblader/i3): Tiling window manager. 
* [i3blocks 1.4-6](https://github.com/vivien/i3blocks): Configurable blocks for i3 status bar. WITH i3-gaps. TODO.
* [i3status 2.12-1](https://github.com/i3/i3status): Status bar fr i3. WITH i3-gaps.
* [intel-ucode 20180807-1](https://downloadcenter.intel.com/search?keyword=processor+microcode+data+file): intel CPU microcode updates without need the BIOS/UEFI 
* [irssi 1.1.1-2](https://irssi.org/): IRC CLI client.
* [iw 4.14-1](https://wireless.wiki.kernel.org/en/users/Documentation/iw): Wireless interface manager.
* [jq 1.5-6](https://stedolan.github.io/jq/): JSON processor
* [laptop-mode-tools 1.72.2-3](https://github.com/rickysarraf/laptop-mode-tools): Laptop power saving package.
* [neovim 0.3.1-1](https://neovim.io/): Modal text editor.
* [ntfs-3g 2017.3.23-1](https://www.tuxera.com/community/open-source-ntfs-3g/): Read-write NTFS driver. Used for the shared windows data partition.
* [openssh 7.7p1-2](https://www.openssh.com/): Remote login tools based wiht SSH protocol.
* [os-prober 1.76-1](http://joeyh.name/code/os-prober/): Bootloader script for probing other OS partitions. WITH grub2.
* [ripgrep 0.9.0-1](): 
* [rofi 1.5.1-1](): 
* [rofi-dmenu 1-1](): 
* [thefuck 3.27-2](): 
* [trizen 1:1.51-1](): 
* [unzip 6.0-12](): 
* [visual-studio-code-bin 1.25.1-3]():  
* [wireless_tools 30.pre9-2](): 
* [xclip 0.13-1](): 
* [xf86-video-intel 1:2.99.917+831+ge7bfc906-1](): 
* [xfsprogs 4.17.0-1](): 
* [xorg-server 1.20.0-9](): 
* [xorg-xinit 1.4.0-3](): 
* [xorg-xrandr 1.5.0-1](): 
