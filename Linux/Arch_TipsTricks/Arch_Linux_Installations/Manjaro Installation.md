

I for myself usually alwayse use the Manjaro KDE https://manjaro.org/downloads/official/kde/ version. Because for me i like the feature richness of kde but the ease of use of manjaro plus the rolling release model. However, this is to your personal preference. There are many online ressources for that which can be consulted. 

## Installation Steps
### Partitioning

* Apart from Partitioning the installation using the manjaro installer is straight forward. As partitioning scheme I use a btrfs filesystem. When using btrfs an efi partition is needed, mounted at /boot/efi with mark boot.
* Secure boot must be disabled
* A extensive guide on btrfs with arch is found here https://wiki.archlinux.org/title/btrfs

  Guide to install and set up Arch on a bootable device

# Arch Linux From Scratch 
# Dependencies

## General

```bash

# AUR package manager

paru

  

# Splash and Display manager

plymouth # See wiki, set theme and use quiet boot

# gdm-plymouth # Now renamed to gdm-plytmouth-prime 

sddm
  

# Keyring and security

lxsession # Polykit auth agent

gnome-keyring

libsecret

kwallet-pam # needed for kmail

kwalletmanager # set empty passwd to unlock automatically

  

# Audio

pipewire

pipewire-audio

pipewire-pulse

pipewire-alsa

pipewire-jack

pipewire-zeroconf

wireplumber

playerctl

  

# Utils (needed by bars, scripts)

network-manager-applet

dex # Autostarter

python-dbus

xdg-user-dirs # enable service

  

# Fonts

ttf-dejavu

ttf-font-awesome-6

ttf-jetbrains-mono-nerd

ttf-fira-code

ttf-inconsolata

ttf-liberation

ttf-firacode-nerd

ttf-indic-otf

ttf-roboto

ttf-iosevka-nerd

noto-fonts

noto-fonts-emoji

noto-fonts-cjk

  

# Apps

sublime-text # Add the Sublime Text mirror first

sublime-merge

intellij-idea-ultimate-edition

jdk17-openjdk

clang

cmake

  

alacritty

nemo

nemo-fileroller

nemo-terminal

nemo-compare

gvfs-smb # needed for nemo

file-roller

udisks2

ntfs-3g

evince

hunspell # Spellcheck for kmail

hunspell-de

hunspell-en_us

kdepim-addons # Plugins for KMail

kmail

korganizer

kdeconnect

indicator-kdeconnect

kde-cli-utils

  

# Themes and Theme-setter

papirus-icon-theme-git

arc-gtk-theme-git

arc-kde-git

qt5ct-kde

kvantum-qt5

lxappearance

  

zsh

ack

fzf

exa

  

teams

#zoom

slack-desktop

signal-desktop

teamspeak

discord

  

seafile-client

  

intel-ucode

amd-ucode

docker

  

cups

cups-pdf

system-config-printer

brother-hll2350dw

  

bluez

bluez-utils

blueman

  

inetutils

python-pip

  

# JSON parser for scripts

jq

python-jq

  

# Used to control display brightness

# !! Add user to video group: sudo gpasswd -a lucas video

light

  

# From own repos

wp-notifyd

```
## Misc

```
sudo pacman -S htop obsidian micro 
```
## Wayland

```bash

xdg-desktop-portal-gtk # needed since -wlr does not implement everything

xdg-desktop-portal-hyprland-git

sway

hyprland

swaylock

swayidle

swaybg

rofi-lbonn-wayland-git

grim # for flameshot

flameshot

waybar

slurp # To select a screen when sharing https://wiki.archlinux.org/title/PipeWire#WebRTC_screen_sharing

qt5-wayland

qt6-wayland

workstyle # Auto rename workspaces with icons

wl-clipboard # for screenshots and scripts

wlogout

gammastep

xorg-xwayland

```

  

# Shell

```bash

chsh

# Select /bin/zsh

```

  

# Theming

Set the theme using qt5ct and lxappearance when using X. Or change ~/.config/gtk-3.0/settings.ini and sway config (the gsettings commands) on wayland.

  

# Automount

  

See https://github.com/coldfix/udiskie/wiki/Permissions

See https://gist.github.com/Scrumplex/8f528c1f63b5f4bfabe14b0804adaba7

  

udiskie and dophin requires permission for some polkit actions which are usually granted when using a desktop environment (for using udisks2). If your login session is not properly activated you may need to customize your polkit settings. Create the file /etc/polkit-1/rules.d/50-udiskie.rules with permissions 644, and with the following contents:

  

```

// Original rules: https://github.com/coldfix/udiskie/wiki/Permissions

// Changes: Added org.freedesktop.udisks2.filesystem-mount-system, as this is used by Dolphin.

  

polkit.addRule(function(action, subject) {

var YES = polkit.Result.YES;

// NOTE: there must be a comma at the end of each line except for the last:

var permission = {

// required for udisks1:

"org.freedesktop.udisks.filesystem-mount": YES,

"org.freedesktop.udisks.luks-unlock": YES,

"org.freedesktop.udisks.drive-eject": YES,

"org.freedesktop.udisks.drive-detach": YES,

// required for udisks2:

"org.freedesktop.udisks2.filesystem-mount": YES,

"org.freedesktop.udisks2.encrypted-unlock": YES,

"org.freedesktop.udisks2.eject-media": YES,

"org.freedesktop.udisks2.power-off-drive": YES,

// Dolphin specific

"org.freedesktop.udisks2.filesystem-mount-system": YES,

// required for udisks2 if using udiskie from another seat (e.g. systemd):

"org.freedesktop.udisks2.filesystem-mount-other-seat": YES,

"org.freedesktop.udisks2.filesystem-unmount-others": YES,

"org.freedesktop.udisks2.encrypted-unlock-other-seat": YES,

"org.freedesktop.udisks2.eject-media-other-seat": YES,

"org.freedesktop.udisks2.power-off-drive-other-seat": YES

};

if (subject.isInGroup("storage")) {

return permission[action.id];

}

});

```

  

(Optional) add group `storage`

Add user to group `storage`:

```

sudo gpasswd -a <user> storage

```

  

# Wallet

  

- Use KWallet for Akonadi

- Use GNOME Wallet for everything else

  

Add both to /etc/pam.d/login

Add GNOME to /etc/pam.d/passwd. This updates the password automatically.

  

Use an empty password in KWallet to allow auto-unlock and forgetting about it.

  

# Windows Compatibility

Windows uses localtime by default arch uses UTC. Change Windows to UTC:

```

reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\TimeZoneInformation" /v RealTimeIsUniversal /d 1 /t REG_DWORD /f

```

  

# T480s

  

## Dynamic Plattform (Throttle FIX)

  

```

pacman -S throttled

# Maybe need to install manually from Github

sudo systemctl enable --now throttled.service

```

  

## Reverse Scrolling (X11 only)

  

Add a file `/etc/xorg.conf.d/30-touchpad.conf` with:

```

Section "InputClass"

Identifier "Elan Touchpad"

Driver "libinput"

MatchIsTouchpad "on"

Option "NaturalScrolling" "true"

EndSection

  

```

  

## Detect lid close (X11 only)

Edit `etc/acpi/handler.sh` to automatically run `autorandr`:

```

# ...

button/lid)

systemctl start --no-block autorandr.service

# ...

```

  

## NVIDIA Graphics

See [here](https://wiki.archlinux.org/title/nvidia-xrun).

  

### Switching

  

Install `nvidia, nvidia-utils, nvidia-xrun-git`.

  

Run

```

# systemctl enable nvidia-xrun-pm

```

  

Copy all files from `/etc/X11/xorg.conf.d` to `/etc/X11/nvidia-xorg.conf.d`.

  

Start session in tty != 1 (because startx is executed here) with

```

nvidia-xrun

```

  

Bug-Fixes

See https://aur.archlinux.org/packages/nvidia-xrun#comment-835925

https://github.com/Witko/nvidia-xrun/issues/160

  

- Segmentation faults: edit `/etc/X11/nvidia-xorg.conf`:

```

Section "ServerLayout"

Identifier "layout"

- Screen 1 "nvidia"

+ Screen 0 "nvidia"

Inactive "intel"

EndSection

  

```

  

- Also placing 10-nvidia-drm-outputclass.conf from https://wiki.archlinux.org/title/NVIDIA_Optimus#Use_NVIDIA_graphics_only in /etc/X11/nvidia-xorg.conf.d sets NVIDIA as default

  

10-nvidia-drm-outputclass.conf:

```

Section "OutputClass"

Identifier "intel"

MatchDriver "i915"

Driver "modesetting"

EndSection

  

Section "OutputClass"

Identifier "nvidia"

MatchDriver "nvidia-drm"

Driver "nvidia"

Option "AllowEmptyInitialConfiguration"

Option "PrimaryGPU" "yes"

ModulePath "/usr/lib/nvidia/xorg"

ModulePath "/usr/lib/xorg/modules"

EndSection

```

  

### Turn of completely

See https://wiki.archlinux.org/title/Hybrid_graphics#Using_udev_rules

  

## Battery saving

Install `tlp` and for Thinkpads also `acpi_call`. Optionally disbale nvidia graphics.

  

## Washed out colors (X11)

Mainly on intel CPUs

  

```

xrandr --output <OUT> --set "Broadcast RGB" "Full"

```

  

## Restore Backlight

https://wiki.archlinux.org/title/backlight#Save_and_restore_functionality

  

# Pipewire

  

Config file is `/usr/share/pipewire/pipewire.conf`!

Local config is `~/.config/pipewire`!

  

Make sure `pipewire-media-session` is uninstalled and `wireplumber` is installed. Enable using:

```bash

systemctl --user --now enable wireplumber

```

  

Note: Wireplumber is the pipewire media-session.

  

Now use `wpctl` to control audio.

```bash

wpctl set-deafault <ID>

```

  

## Set sample rate for pipewire pipeline

```bash

mkdir -p ~/.config/pipewire

cp cp /usr/share/pipewire/pipewire.conf ~/.config/pipewire/pipewire.conf

  

# Edit default.clock.rate and default.clock.allowed-rates

nano ~/.config/pipewire/pipewire.conf

```

  

## Set default sample rate for a output device

```bash

# Get device id

wpctl list

# Search for node.name

wpctl inspect <ID>

  

# If not present

mkdir -p ~/.config/wireplumber/main.lua.d

cp /usr/share/wireplumber/main.lua.d/50-alsa-config.lua ~/.config/wireplumber/main.lua.d/50-alsa-config.lua

  

# Add a new entry in the rules array

nano ~/.config/wireplumber/main.lua.d/50-alsa-config.lua

```

  

As far as I understand the `pipewire.conf` sets the sample rate for the pipeline and the `50-alsa-config.lua` the sample rate of the output. That means resampling is necessarry when entering the pipeline and when exiting.

I set the output to a high rate if it is a high-quality DAC and use `allowed-rates` in the `pipewire.conf` to use the native sample rate of the audio.

  

## Stream to Airplay

https://gitlab.freedesktop.org/pipewire/pipewire/-/issues/1542

  

```

paru -Sy avahi openssl readline

paru -Sy pipewire-zeroconf

pactl load-module module-raop-discover

  

```

  

## Output over Network

  

Currently only supported by the most current version! (Use `pipewire-git`).

Then enable the necessary modules:

  

```bash

# Requrements openssl, avahi

sudo systemctl enable --now avahi-daemon.service

pactl load-module module-raop-discover

```

  

# Secure Boot

Follow the sbctl guide on the [Arch Wiki](https://wiki.archlinux.org/title/Unified_Extensible_Firmware_Interface/Secure_Boot#sbctl).

  

# Boot process

  

## Do not wait for a network connection

```bash

systemctl disable NetworkManager-wait-online.service

```

  

## Use `.socket` instead of `.service`

One central feature of systemd is D-Bus and socket activation. This feature should be preferred for most cases as it causes services to be started only when they are first accessed and is generally a good thing (e.g. having cups.service enabled at boot time is usually not useful for desktop use, enable instead cups.socket which will only start the service when actually printing).

  

However, if you know that a service (like upower) will always be started during boot, then the overall boot time might be reduced by starting it as early as possible. This can be achieved (if the service file is set up for it, which in most cases it is) by enabling upower.service.

  

This will cause systemd to start UPower as soon as possible, without causing races with the socket or D-Bus activation.

  

# Silent Boot

  

Go to `/boot/loader/entries/yourentry` and add `quiet` to the options.

  

# GDM disable suspend

  

Run

```bash

sudo -u gdm dbus-launch gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-ac-type 'nothing'

```

  

if that does not work try:

```bash

# Login as the gdm user

machinectl shell gdm@ /bin/bash

gsettings set org.gnome.desktop.session idle-delay 0

```

  

# Wayland VNC

  

Just start `wayvnc <ipaddress>`.

To initiate the session from SSH use:

  

```bash

# wlr_comositor being sway or Hyprland

WLR_BACKENDS=headless WLR_LIBINPUT_NO_DEVICES=1 wlr_compositor

# Then connect a second time and start wayvnc

WAYLAND_DISPLAY=wayland-1 wayvnc <ipaddress>

```

## Setup automatic snapshots (BTRFS only) with snapper

https://www.dwarmstrong.org/btrfs-snapshots-rollbacks/