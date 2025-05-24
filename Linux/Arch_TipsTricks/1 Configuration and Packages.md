# 1 Configuration and Packages

## 1.1 Package Manager

Update Manjaro and select fastest mirror

```sh
$ sudo pacman-mirrors --fasttrack
```

Afterwards a full system update is recommended:

```sh
$ sudo pacman -Syyu
```

Arch has a large repository for "inoficial" software called **AUR**. To install packages from AUR the different approaches are feasbible:

1. Cloning the repository from AUR and compiling the package using makepkg. Fur this purpose inside cloned AUR repo do

	```sh
	$ makepkg -si
	```

Note that no root user is needed for this and makepkg can be installed with the package **base-devel**.

2. A more convenient way is to use a package manager that has AUR incorporated. I recommend paru or yay. yay can be found in the official repos (`using sudo pacman -S yay`). Paru needs to be installed from AUR

	```sh
	$ git clone https://aur.archlinux.org/paru.git && cd paru && makepkg -si
	```

As of now this does yield an error because rust needs to be configured using.

```sh
$ rustup update stable && rustup default stable
```

Sometimes packages updates are lagging a little bit behind the official pacman repositories (The reason ist that manjaro takes itself some time to test the updates which result in a more stable experience compaired to vanilla arch). Thus if you are dependent on a new package and cannot wait you can change the upstream mirror 

```sh
$ sudo pacman-mirrors --api --set-branch testing 
```

After that reset the mirror list and update 

```sh
$ sudo pacman-mirrors --fasttrack && pacman -Syyu
```

However be careful because this may break your system.

### 1.1.1 General Settings for Pacman

Edit the pacaman Conf

```sh
$ sudo nano /etc/pacman.conf
```

Add the following lines (if not already in the conf)

```sh
Color
ParallelDownload=5
```

Additionally BottomUp can be uncommented when using paru in /etc/paru.conf. This greatly increases the download speed and make the console output more readable

## 1.2 Typical packages on my system

### 1.2.1 File Explorer

I prefer doublecommander

```sh
$ sudo pacman -S doublecmd
```

other alternatives (if not already availabl on your system) are ranger,
nautillus or dolphin.

#### Configure

* **Show hidden files**: `Configuration > Fileview > Show hidden and system files`

### 1.2.2 Terminal Emulator and shell

**Shell**

I prefer zsh over bash, because zsh has a great tool called oh-my-zsh https://github.com/ohmyzsh/ohmyzsh. This enhances zsh by a lot of tools. In newer manjaro kde versions zsh is the standard tool. The github link provides a good installation guide for both zsh and oh-my-zsh.

**Terminal Emulator**

I prefer one of **kitty, terminator** or the standard terminal installed in manjaro kde **konsole** all can be installed from the official repositories

## 1.2.3 Developing

__Editors/IDES__:
* **VSCODE**: `$ pacman -S code` Because i use setting sync we need to use the AUR binary https://aur.archlinux.org/packages/visual-studio-code-bin/
* **Pycharm**: `$ pacman -S pycharm-community-edition` or from snap https://snapcraft.io/install/pycharm-community/arch
* **Micro**: An lightweight terminal editor https://micro-editor.github.io/ `$ pacman -S micro`
* **R-Studio**: The best ide for Rlang `$ paru -S rstudio-desktop` 

__Languages__:

* Rust: `$ curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh`

### 1.2.4 Multimedia

* **Spotify**: AUR only for arch. Because of a wrong GPA key consider using makepkg -si --skip-checksums
* **Telegram**: `$ sudo pacman -Sy telegram-desktop`
* **Discord**: `$ sudo pacman -Sy discord`

### 1.2.5 Browsers

I prefer chromium based browser because of the working netflix 1080p plugin. 

* **Vivaldi** `$ pacman -S vivaldi vivaldi-ffmpeg-codecs`
* **Firefox** `$ pacman -S firefox`
* **Chromium** `$ pacman -S chromium`

### Cli Tools

* __Tmux__: For remote sessions (ssh)
* __fzf__: For fuzzy autocompletion and reverse searching. Note for bash you have to source it in your `.bashrc` https://wiki.archlinux.org/title/fzf