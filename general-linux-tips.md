
# General tips for Linux

This document contains several tips and trick when using Linux, which might make your life easier.



[TOC]

## Keyboard settings

### Disable caps lock

For me, the caps lock key ist just an unnessesarc relic from the old ages of computing. I don't like it and I don't need it.

```bash
setxkbmap -option caps:none
```

This will not be enabled by default on startup. To enable it, add it to you `~/.profile` or `~/.bash_profile` file.

```bash
echo 'setxkbmap -option caps:none' >> ~/.profile
```



### Set US keyboard layout, intl. with dead keys

This only applies to the current session, as this is not always needed.

```bash
setxkbmap -model pc105 -layout us -variant altgr-intl 
```



## Verify checksums

```bash
md5sum
sha1sum
sha256sum
```




## Mount USB device and allow all users to read and write

```bash
sudo mount -o gid=users,fmask=113,dmask=002 /dev/sdc1 /run/media/simon/
```



## Enable NTFS support and automount devices

You need to install the `udisks2` and `ntfs-3g` packages. `udiskie` is also recommended as a frontend to `udisks2`.
Maybe you need to enable automounting in your file manager of choice, like `Thunar` or `Nautilus`.



## Change Fonts in Alacritty

Install the font with pacman or from source, then change the font in the `alacritty.yml`, for example to `RobotoMono`. 



## Install Vim Plugins

First, clone the `pathogen` git repo.

```bash
mkdir -p ~/.vim/autoload ~/.vim/bundle
curl -LSso ~/.vim/autoload/pathogen.vim https://tpo.pe/pathogen.vim
```

Add the following line to your `.vimrc`.

```bash
execute pathogen#infect()
```

Then, clone the `lightline` git repos.

```bash
git clone https://github.com/itchyny/lightline.vim ~/.vim/bundle/lightline.vim
```

Add the following lines into you `.vimrc`.

```bash
let g:lightline = { 'colorscheme': 'powerline`, }
set laststatus=2
set noshowmode
```

To install [Pydiction](https://rkulla.github.io/pydiction/), clone the git repo into your vim folder:

```bash
cd ~/.vim/bundle
git clone https://github.com/rkulla/pydiction.git
```

Then, add the following to your .vimrc:

```vim
filetype plugin on
let g:pydiction_location = '/home/user/.vim/bundle/pydiction/complete-dict'
let g:pydiction_menu_height = 3
```



## Change Cursors

Install the cursor package first, eg `capitaine-cursors`.

Then change the `Inherits=` line in `/usr/sahre/icons/default/index.theme` to your cursor.



## Install fonts

Either install the font directly with pacman, or install it manually.
Download the font file (`.ttf`, `.otf` or others) and copy them to `/usr/share/fonts`.
Check if your font is recognized with `fc-list | grep <your-font>`. If it is listed, you can proceed.
Then update the font cache with `fc-cache`. If this doesn't work, you can use `fc-cache -f -r` to force a scan.



## Zathura not displaying pdf

If zathura displays an error when trying to open a pdf, install the `zathura-pdf-poppler` package

```bash
error: could not open plugin directory: /usr/lib64/zathura
error: unknown file type
```



## Disable Middle Click paste

Install the `xbindkeys xsel xdotool` packages.

place this is `~/.xbindkeysrc`.

```bash
"echo -n | xsel -n -i; pkill xbindkeys; xdotool click 2; xbindkeys"
b:2 + Release
```

Reload the configuration with `xbindkeys -p`.
Run `xbindkeys` on startup, `pkill xbindkeys` to stop.



## Change bash prompt color

```bash
export PS1='\[\e[31m\][\[\e[33m\]\u\[\e[32m\]@\[\e[34m\]\h \[\e[35m\]\W\[\e[31m\]]\[\e[00m\]\$ '
```

This sets the color to purple, and leaves the prompt text as is (user@host).
To make this permanent, add it to your `~/.bashrc` file.



## List all enabled systemctl programs/daemons

```bash
systemctl list-unit-files
```



## Add missing key to keyring

Sometimes needed for yay or pacman

```bash
gpg --keyserver pool.sks-keyservers.net --recv-keys [your key]
```



## Set Monitor Layout with X11 config file

```bash
Section "Monitor"
	Identifier	"DP-0"
	Option		"Primary" "true"
EndSection

Section "Monitor"
	Identifier	"HDMI-0"
	Option		"LeftOf" "DP-0"
EndSection
```
