# General

## Multi Monitor setup wih Tiling Window Managers

Because of HDMI add the following to .xinitrc `xrandr --output HDMI2 --auto --right-of eDP1`

## Increase Font Size 

Especially for small monitors the standard is way to small, edit `~/.Xressources` &Rightarrow; `Xft.dpi: 110` as described here for i3-wm https://unix.stackexchange.com/questions/267885/how-do-i-scale-i3-window-manager-for-my-hidpi-display

# Manjaro i3 edition (XFCE)

This tutorial has some nice additional ressources for i3 additionally for ubuntu, but should also work on arch https://github.com/alec-kr/i3-rice or https://github.com/steelvelveteen/i3_dotfiles (which is more targeted on arch)

* Polybar: A nicer status bar than i3-status (yay install polybar from AUR)
  * For some icons font-awesome needs to be downloaded: `yay ttf-font-awesome`
* i3-gaps -> Not needed on manjaro-i3 (already installed)
* kitty: Terminal emulator `sudo pacman -S kitty` -> Use the kitty.conf from the first rice
* rofi: Replacement for dmenu (Application Launcher i.e. mod+d) `sudo pacman -S rofi` &Rightarrow; https://wiki.archlinux.org/title/Rofi (Then copy the config files from the second rice) &Rightarrow; Binding does not work right now need to find error -> Error was double binding of mod+d
  * Exec command: `rofi -show drun -theme sidebar -show-icons`
  * Exec command windows switcher:`rofi -show window -theme sidebar -show-icons`

### ZSH
* zsh/ oh-my-zsh (Manjaro comes preinstalled with zsh you just have to set it as default )
* Set as default `sudo chsh -s zsh`
* Install oh-my-zsh for arch https://github.com/ohmyzsh/ohmyzsh
* Many themes require the powerline fonts to be installed https://github.com/powerline/fonts  (which is already installed on manjaro)

### Additional Information
__Multi Monitor setup__
* In my current setup there is a bug with xserver and startx with prevents the xrandr code needed for dual Monitor setup to be executed correctly. A quick fix was to write this code into .bashrc or .zshrc. Does after startup a console can be opened and the xrandr command is called. The command used is  
```
con=$(xrandr -q | grep HDMI2 | grep -w -o connected)
if [ $con = 'connected' ]; then
    xrandr --output eDP1 --primary --mode 1920x1080 --rate 120.00 --output HDMI2 --mode 1920x1080 --right-of eDP1
else
    xrandr --output eDP1 --primary --mode 1920x1080 --rate 120.00
fi
```
* You have to change the outputs if you have another device 

# Bspwm setup on Manjaro KDE

The following software is used for my bswpm setup:

* **Conky**: As system monitor 
* **Rofi**: For Window switching and programm starter
* **Sxhkd**: For shortcut settings
* **bswpwm**: As window manager
* **Plasma**: All tasks the window manager is not fulfilling (Bars, symbols, ...)
* **Picom**: Compositor for bluring effects, because kwin is also the compositor in KDE. 
  
Use the dotfiles and Scripts from this repository https://github.com/Banjio/ManjaroRices1312/tree/main/kde-plasma-bspwm

More information on how to run Plasma with Bspwm is found in this tutorial https://www.christitus.com/bspwm-on-kde/

Install the software needed with: 

    > sudo pacman -S bspwm rofi kitty conky sxhkd picom

* Move all config files from the repository to ~/.config/
* Add a file to /usr/share/xsessions/ named plasma-bspwm.desktop with the Content
```
[Desktop Entry]
Type=XSession
Exec=env KDEWM=/usr/bin/bspwm /usr/bin/startplasma-x11
TryExec=/usr/bin/startplasma-x11
DesktopNames=KDE
Name=Plasma bspwm
```

### Additional Information for the used software 

**Conky**

* Take config from dotfiles -> If it is not workings as extended run conky in Debug Mode `conky -D`
* Note that it may be neccessary to remove the conky call from bspwmrc

**Rofi**

* Basicially needs no extra config (But you can also configure it to your wishes)
  * As runer: `rofi -show run -theme sidebar -show-icons`
  * As Druner: `rofi -show drun -theme sidebar -show-icons`
  * As windows switcher:`rofi -show window -theme sidebar -show-icons`

**Change Wallpaper**

* Not working on BSWPM with plasma
* Workaround is to change the wallpaper in an Plasma Session (Or use a manage for example feh)

**Picom**

* Needed for bluring, transparency ... effects. Because kwin does both the window management and the compositing you need an own compositor. Picom works just fine. 
  * Install: `sudo pacman -S picom`
  * Start with bspwm:  Add to bswpmrc &Rightarrow; exec picom -b


## Bugs

Bug on BSPWM prevents vscode from correctly closing, but it is not apprearing anywhere in the opened processes -> Use `> code -r` to open existing vscode instances


# Known Problems and Solutions

