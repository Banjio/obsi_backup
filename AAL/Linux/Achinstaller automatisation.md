Notes for installing arch with the python-archinstaller for fast setup. Afterwards great part of the system should be setup with bash scripts. 
This can be further automatized by supplying a config to the installer which has all the settings embedded into it https://python-archinstall.readthedocs.io/en/latest/installing/guided.html#installing-directly-from-a-configuration-file
# Install arch with the script
https://wiki.archlinux.org/title/Installation_guide
1. Boot into a live medium and run archinstall 
2. Secure Boot needs to be disabled during installation but can be reactivated afterwards
3. Load german keyboard layout `loadkeys de-latin1`
4. Verify boot mode `cat /sys/firmware/efi/fw_platform_size` should return 64hyprlan
## Post install
1. Install packages Run the installPkgs.sh Script
```bash 
# Refresh mirrors and upgrade System

sudo pacman -Syyu

# Install paru so that we can install all using paru

sudo pacman -S --needed base base-devel

git clone https://aur.archlinux.org/paru.git

cd paru

makepkg -si

  

# System and Misc

paru -Sy intel-ucode amd-ucode zsh ack fzf exa docker cups cups-pdf system-config-printer bluez bluez-utilsblueman inetutils python-pip

  

# Splash and Display manager

paru -Sy plymouth gdm-plymouth

  

# Keyring and security

paru -Sy lxsession gnome-keyring libsecret

  

# Audio

paru -Sy pipewire pipewire-audio pipewire-pulse pipewire-alsa pipewire-jack pipewire-zeroconf wireplumber playerctl

  

# Utils (needed by bars, scripts)

paru -Sy network-manager-applet dex python-dbus xdg-user-dirs jq python-jq # enable service

  

# Fonts

paru -Sy ttf-dejavu ttf-font-awesome-6 ttf-jetbrains-mono-nerd ttf-fira-code ttf-inconsolata ttf-liberation ttf-firacode-nerd ttf-indic-otfttf-roboto ttf-iosevka-nerd noto-fonts noto-fonts-emoji noto-fonts-cjk

  

# Apps

paru -Sy clang cmake nemo-fileroller nemo-terminal nemo-compare file-roller udisks2 ntfs-3g evince hunspell hunspell-de hunspell-en_us firefox

  

# Own Apps

paru -Sy terminator firefox vlc hyprshot xviewer spotify obsidian micro doublecmd-qt5 htop visual-studio-code-bin downgrade xournalpp discord

  

# Themes and Theme-setter

paru -Sy papirus-icon-theme-git arc-gtk-theme-git arc-kde-git qt5ct-kde kvantum-qt5 lxappearance

  

# Display and Window Manager (Hyprland)

paru -Sy xdg-desktop-portal-gtk xdg-desktop-portal-hyprland-git sway hyprland swaylock swayidle swaybg rofi-lbonn-wayland-git waybar slurp qt5-wayland qt6-wayland workstyle wl-clipboard wlogout gammastep xorg-xwayland

  

# Optional

# paru -Sy teams zoom slack-desktop signal-desktop seafile-client
```
`
2. Setup automatic btrfs snapshots
```bash
#  Snapper and snap-pac for automatiC Snapshots
paru -Sy snapper snap-pac
# Create snapshot configuration for root sobvolume
sudo umount /.snapshots
sudo rm -rf /.snapshots
sudo snapper -c root create-config /
# Setup /.snapshots
sudo btrfs subvolume list /
sudo btrfs subvolume delete .snapshots
sudo mkdir /.snapshots
sudo mount -a
sudo chmod 750 /.snapshots
# Optional automatic timeline snaphots see point 5 https://www.dwarmstrong.org/btrfs-snapshots-rollbacks/
```
3.  Setup Docker (Add to userGrouop)
4. Install Programming specific software
```bash 
paru -Sy npm 
npm install typescript -g --save-dev
npm install -g sass
```