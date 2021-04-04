# Things to do after a successfull Arch install

Congratulations, you have successfully installed Arch Linux!
After the you might wonder, what to do now.
This guide provides some useful steps you might want to take after a clean Arch install.
Please read all the steps before executing them, as you might not need all of them.

## Add a user

After a plain Arch install there is only the root user. Since you don't want to be logged in as root all the time, we need to add another regular user.

Edit the `sudoers`-file at `/etc/sudoers` with vim or nano.

```sh
sudo EDITOR=vim visudo
```

Uncomment the line that says `%wheel ALL=(ALL) ALL`.
Then, we add a new user called `simon` to the `wheel` group we just uncommented.The user gets his own `/home` directory.

```sh
useradd -m -G wheel -s /bin/sh simon
```

As a last step, the user needs a password:

```sh
passwd simon
```

Enter a password and confirm it.

## Install yay (AUR package manager)

[Yay](https://github.com/Jguer/yay) is a package manager, that enables you to install and maintain packages from the AUR (Arch User Repository).
First, install git:

```sh
sudo pacman -S git
```

Then, clone the git repository of yay:

```sh
git clone https://aur.archlinux.org/yay.git
```

Navigate into the yay directory:

```sh
cd yay
```

And install the Package

```sh
makepkg -si
```

You might want to remove the build dependency `Go` afterwards:

```sh
pacman -Rns go
```

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

## Install a graphical user interface (GUI)

A Graphical userinterface is a convenient way to interact with the computer, so you don't have to run everything in the terminal.
There are several GUIs available for linux. We differentiate between display managers and desktop environments.
In this case, we install the GNOME desktop environment. For other desktop environments, please consult the Arch Wiki.

### Install display Server

First, we need a display server:

```sh
pacman -S xorg-server xorg-xinit
```

Alternatively, you can install wayland as a display server, but you might run into problems with some programs.

### Install display drivers

For optimal use of our system resources, a display driver is required.

```sh
sudo pacman -S xorg-drivers
```

### Install Awesome with lightdm

```sh
sudo pacman -S lightdm lightdm-gtk-greeter awesome
sudo systemctl enable lightdm.service
```

## Install some useful utilities

```sh
sudo pacman -S unzip gvfs udisks2
```

## Install useful programs

```sh
sudo pacman -S thunar moc
yay -S qview #or nomacs

sudo pacman -S darktable
sudo pacman -S opencl-nvidia ocl-icd # OpenCL support for Darktable
```

## Network utilities

```sh
pacman -S dnsutils net-tools
pacman -S wireshark-cli metasploit postgresql nmap
```

