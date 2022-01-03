# Things to do after a successfull Arch install

This document is more of a collection of packages for me, always consult the Arch Wiki in case of uncertainty.

## Add a user

After a plain Arch install there is only the root user. Since you don't want to be logged in as root all the time, we need to add another regular user.

Edit the `sudoers`-file at `/etc/sudoers` with vim.

```sh
sudo EDITOR=vim visudo
```

Uncomment the line that says `%wheel ALL=(ALL) ALL`.  
There you can also disable the password prompt for sudo.  
Then, we add a new user called `simon` to the `wheel` group we just uncommented.  

```sh
useradd -m -G wheel -s /bin/zsh simon
```

As a last step, the user needs a password:

```sh
passwd simon
```

Enter a password and confirm it.

## Install yay (AUR package manager)

[Yay](https://github.com/Jguer/yay) is a package manager, that enables you to install and maintain packages from the AUR.  
Check the github repo for installation instructions.

## Install Nvidia drivers

If you have an Nvidia graphics card, install the Nvidia drivers:

```sh
sudo pacman -S nvidia # or
sudo pacman -S nvidia-lts   # for use with the LTS-kernel
```

## Install and enable some useful services

Some services are really usefull for resource and disk management.

```sh
sudo pacman -S acpid ntp dbus avahi cups cronie
```

Then, enable these services, so they get automatically started at boot.

```sh
systemctl enable acpid
systemctl enable ntpd
systemctl enable avahi-daemon
systemctl enable org.cups.cupsd.service
```

## Install sound server

```sh
sudo pacman -S pulseaudio alsa-utils pavucontrol pulseaudio-alsa
```

This installs the pulseaudio sound server and the alsa mixer, a tool within the terminal to control different sound channels. It can also be controlled by pavucontrol. Restart the system after pulseaudio is installed.

## Install a desktop environment
### Install display Server

First, we need a display server:

```sh
pacman -S xorg-server xorg-xinit
```

### Install display drivers

For optimal use of our system resources, a display driver is required.

```sh
sudo pacman -S xorg-drivers
```

### Install Awesome and related utilities

```sh
sudo pacman -S awesome rofi slock alacritty
```

## Install some useful utilities

```sh
sudo pacman -S unzip gvfs udisks2 ack sed
```

### Install useful programs

```sh
sudo pacman -S thunar moc
yay -S qview

sudo pacman -S darktable
sudo pacman -S opencl-nvidia ocl-icd # OpenCL support for Darktable
yay -S colorpicker brave-bin
```

### Network utilities

```sh
pacman -S dnsutils net-tools
pacman -S wireshark-cli metasploit postgresql nmap
```

#### Use dnsmasq with NetworkManager

Install `dnsmasq` and configure `NetworkManager` to use it as a dns service (see the [Arch Wiki](https://wiki.archlinux.org/title/NetworkManager#dnsmasq)).  
Put your config into `/etc/NetworkManager/dnsmasq.d/` and restart the service.

### Phone support

Enables file browser support for Android phones (reboot for changes to take effect).
```sh
pacman -S mtpfs gvfs-mtp
yay -S jmtpfs
```

# Clean Arch Linux

## Pacman
Command | Action
--- | ---
`pacman -Sc` | removes the cache for uninstalled packages, alternatively use a [hook](https://github.com/sihensel/dotfiles/blob/main/clear_cache.hook)
`pacman -Qdtq` | lists all unused packages
`pacman -Rnscd $(pacman -Qdtq)` | removes all unused packages

## Cache
Command | Action
--- | ---
`du -sh ~/.cache` | shows the size of the cache folder
`rm -rf ~/.cache/*` | deletes everything from that folder

## Configs
Check for files and folders in `~/.config` and `~/.local/share` that are no longer needed.

## Clean Systemd journal
Set a limit for `journald` with `journalctl --vacuum-time=2weeks` or `journalctl --vacuum-size=50M` or delete the logs manually.  
To set a permanent limit, set `SystemMaxUse=100M` in `/etc/systemd/journald.conf`.
