# General tips for Linux

This document contains several tips and trick when using Linux, which might make your life easier.

## Keyboard settings

### Disable caps lock

For me, the caps lock key ist just an unnecessary relic from the old ages of computing. I don't like it and I don't need it.

```sh
setxkbmap -option caps:none
```

This will not be enabled by default on startup. To enable it, add it to your shell profile.

```sh
echo 'setxkbmap -option caps:none' >> ~/.profile
```

### Set US keyboard layout, intl. with dead keys

This only applies to the current session, as this is not always needed.

```sh
setxkbmap -model pc105 -layout us -variant altgr-intl 
```

## Verify checksums

Use these commands to generate a checksum of a file using the corresponding algorithm.

```sh
md5sum
sha1sum
sha256sum
```

## Mount USB device and allow all users to read and write

```sh
sudo mount -o gid=users,fmask=113,dmask=002 /dev/sdc1 /run/media/simon/
```

## Enable NTFS support and automount devices

You need to install the `udisks2` and `ntfs-3g` packages. `udiskie` is also recommended as a frontend to `udisks2`.  
Maybe you need to enable automounting in your file manager of choice, like `Thunar` or `Nautilus`.

## Change Cursors

Install the cursor package first, eg `capitaine-cursors`.  
Then change the `Inherits=` line in `/usr/sahre/icons/default/index.theme` to your cursor.

## Install fonts

Either install the font directly with pacman, or install it manually.  
Download the font file (`.ttf`, `.otf` or others) and copy them to `/usr/share/fonts`.  
Check if your font is recognized with `fc-list | grep <your-font>`. If it is listed, you can proceed.  
Then update the font cache with `fc-cache`. If this doesn't work, you can use `fc-cache -f -r` to force a scan.  

## Disable Middle Click paste

Install the `xbindkeys xsel xdotool` packages.  
Place this is `~/.xbindkeysrc`.

```sh
"echo -n | xsel -n -i; pkill xbindkeys; xdotool click 2; xbindkeys"
b:2 + Release
```

Reload the configuration with `xbindkeys -p`.  
Run `xbindkeys` on startup, `pkill xbindkeys` to stop.

## List all enabled systemctl programs/daemons

```sh
systemctl list-unit-files
```

## Add missing key to keyring

This is sometimes needed for yay or pacman.

```sh
gpg --keyserver pool.sks-keyservers.net --recv-keys [your key]
```

## Set Monitor Layout with X11 config file

```sh
Section "Monitor"
	Identifier	"DP-0"
	Option		"Primary" "true"
EndSection

Section "Monitor"
	Identifier	"HDMI-0"
	Option		"LeftOf" "DP-0"
EndSection
```
