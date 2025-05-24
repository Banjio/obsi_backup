# Arch installation 
See [[Install Arch on bootable device]] for packages that miss here

Notes for installing arch with the python-archinstaller for fast setup. Afterwards great part of the system should be setup with bash scripts. This can be further automatized by supplying a config to the installer which has all the settings embedded into it https://python-archinstall.readthedocs.io/en/latest/installing/guided.html#installing-directly-from-a-configuration-file

# Install arch with the python installer script

If your in trouble the [https://wiki.archlinux.org/title/Installation_guide](Official Documentation) is a good starting point. 

1. Boot into a live medium and run archinstall 
2. Secure Boot needs to be disabled during installation but can be reactivated afterwards
3. Load german keyboard layout `loadkeys de-latin1`
4. Verify boot mode `cat /sys/firmware/efi/fw_platform_size` should return 64_*

## Post install

1. Install packages by running the installPkgs.sh Script:

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
paru -Sy plymouth sddm

# Keyring and security
paru -Sy lxsession gnome-keyring libsecret

# Audio
paru -Sy pipewire pipewire-audio pipewire-pulse pipewire-alsa pipewire-jack pipewire-zeroconf wireplumber playerctl

# Utils (needed by bars, scripts)
paru -Sy network-manager-applet dex  python-dbus xdg-user-dirs jq python-jq # enable service

# Fonts
paru -Sy ttf-dejavu ttf-font-awesome-6 ttf-jetbrains-mono-nerd ttf-fira-code ttf-inconsolata ttf-liberation ttf-firacode-nerd ttf-indic-otfttf-roboto  ttf-iosevka-nerd noto-fonts noto-fonts-emoji noto-fonts-cjk

# Apps
paru -Sy clang cmake nemo-fileroller nemo-terminal nemo-compare file-roller udisks2 ntfs-3g evince hunspell  hunspell-de hunspell-en_us firefox

# Own Apps
paru -Sy terminator firefox vlc hyprshot xviewer spotify obsidian micro doublecmd-qt5 htop visual-studio-code-bin downgrade xournalpp discord featherpad

# Themes and Theme-setter
paru -Sy papirus-icon-theme-git arc-gtk-theme-git arc-kde-git qt5ct-kde kvantum-qt5 lxappearance 

# Display and Window Manager (Hyprland)
paru -Sy xdg-desktop-portal-gtk xdg-desktop-portal-hyprland-git sway hyprland swaylock swayidle swaybg rofi-lbonn-wayland-git waybar slurp qt5-wayland qt6-wayland workstyle wl-clipboard wlogout gammastep xorg-xwayland

# Optional
# paru -Sy teams zoom slack-desktop signal-desktop seafile-client 
```

4. Install Programming specific software

```bash 
paru -Sy npm 
npm install typescript -g --save-dev
npm install -g sass
```

## Post package installation

1. Change your shell to zsh (or any other but some of the scripts rely on it)

    * Get a list of all shells `chsh -l`
    * Change shell `chsh -s /usr/bin/zsh`
     
2. Enable needed services: 

    * __bluetooth__: `systemctl enable bluetooth.service`
    * __login manager__: `systemctl enable sddm.service` 
    * __Network manager__: `systemctl enable NetworkManager.service`

3. Make all scripts executable needed by waybar or hyprland &Rightarrow; Run `chmod +x path/to/script`:

	* in `~/.config/hypr/scripts`
	* in `~/.config/waybar/scripts`
	* in `~/dotfiles/home/scripts`

## Optional steps post package installation 

1.  Setup Docker on arch (Note that docker on arch is not officially supported and experimental): 

	* Add your user to the user group kvm and docker: `sudo usermod -aG kvm $USER` and `sudo usermod -aG docker $USER`#+
	* Enable docker daemon (is run on startup) `systemctl enable docker` or start each time `systemctl start docker`

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

3. Automatic background changes on startup

There are several software providing this e.g. feh. Lucas just added a line in the appearance.conf for hyperlink which sets the wallpaper on start `exec-once = hyprctl monitors -j | jq ".[] | .name" -r | xargs -I "{}" zsh -c 'swaybg -o "{}" -i $(find ~/Pictures/wallpaper/. -type f | shuf -n1) -m fill &'`. Thus we just need to provide a folder wallpaper with pictures we want as backgrounds. 



## Misc: List of additional steps 

* Activate settings sync firefox
* Activate settings sync vscode

1. __Use swaync as notifier__

# Troubleshooting

1. __Bluetooth and Network not showing in waybar tray__: 

* The most probable cause is that the autostart.sh file is not executed. This is done in `~/.config/hypr/hyprland.conf`. Probably the path is not correct.
* Another cause can be the tray module of waybar. Try apps that should show up if minimized in the tray, e.g. discord. 
* Other causes can be the applets itself. Run `nm-applet` or `bluetooth-applet` and check the error logs. 

2. __Workspaces are not numbered correctly__: 

* This happens if you power up a monitor after you logged into your session. Then the numbering for the new monitor starts at 11 not 6 Either relogging or restarting will solve this. Reasons is that the script is run only once at startup. 
* Another option is to kill all apps on the monitor with wronlgy labelled workspaces and rerun the script under `~/.config/hypr/scripts/setup_workspaces.py` manually

3. Brittle Font for some apps

*  Some apps have terrible font when using fractional scaling (e.g. if you have a dual monitor setup). There is an open issue on github https://github.com/wez/wezterm/issues/2232

