# Arch Linux installation guide

Always refer to the [Arch Install Guide](https://wiki.archlinux.org/index.php/installation_guide).  
This document shows only a few key commands that might not directly be in the Installation Guide.

## Connect to the Internet

Ensure your network interface is listed.

```sh
ip link
```

### Connect via ethernet (recommended)

DHCP for ethernet connections is enabled by default, so just plug in the ethernet cable.  
If you require a static IP you need to state the IP adress and the netmask.

```sh
ip address add 192.168.1.10/24 dev eth0
```

Same applies to wireless interfaces (like wlan0).

### Connect via WIFI

If your wireless connection requires a static IP, follow the steps above.
To scan and connect to a wireless network, use the `wifi-menu`-command and follow the on-screen instructions.  
If this does not work, you need to connect to the WIFI manually.  
First, list all wireless devices.

```sh
iwctl device list
```

We assume, your wifi interface is `wlan0`.
Scan for available wireless networks (this will not output anything).

```sh
iwctl station wlan0 scan
```

Then, list all available networks.

```sh
iwctl station wlan0 get-networks
```

Finally connect to your desired network. Replace 'SSID' with the name of the network.

```sh
iwctl station wlan0 connect 'SSID'
```

If a passphrase is required, you will be promptet to enter it.
For dynamic IPs, run the DHCP daemon:

```sh
dhcpd
```

Afterwards, you should be connected to the internet, check this with a `ping`.  
If you still have no connection, check the resolve configuration.

```sh
vim /etc/resolv.conf
```

Add the DNS server of your router or a public DNS server address.

```sh
nameserver 192.168.1.1
nameserver 0.0.0.0
```

# Install the Arch system

## Create and format the partitions

If you use BIOS, one root partition is enough, if you use UEFI, don't forget to create an EFI-partition first. If you want to encrypt your disk, read on before creating any partitions.

### Encrypting the root partition
Create all partitions but don't create any filesystems yet, as we need to encrypt the root partition first.  
Load the cryptsetup kernel modules:

```sh
modprobe dm-crypt
modprobe md-mod
```

Create 3 partitions: UEFI, boot and root (which will be encrypted) with `fdisk`.  
There should be three partitions:
- `/dev/sda1` - efi, 256 MB
- `/dev/sda2` - boot, 512 MB
- `/dev/sda3` - root, Rest of disk

Now we encrypt the `root` partition.

```sh
cryptsetup luksFormat -v -s 512 -h sha512 /dev/sda3
cryptsetup open /dev/sda3 luks_root
```

`luks_root` will be the name used to access the partition. Afterwards, the partition will be available at `/dev/mapper/luks_root`.  
Now, create a filesystem with `mkfs.ext4 /dev/mapper/luks_root`.
Also create filesystems for the other 2 partitions.
- `/dev/sda1` - efi, vfat
- `/dev/sda2` - boot, ext4

Finally, mount all partitions:
```sh
mount /dev/mapper/luks_root /mnt
mkdir /mnt/boot
mount /dev/sda2 /mnt/boot
mkdir /mnt/boot/efi
mount /dev/sda1 /mnt/boot/efi
```

## Select the mirrors

```sh
pacman -Syy
```

Then install `reflector`.

```sh
pacman -S reflector
```

Make a backup of the mirrorlist (just in case).

```sh
cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.old
```

Now, we will add the ten fastest mirrors of our country to the mirror list. Replace `Germany` with your country. You might want to look up the country codes in the Arch Wiki.

```sh
reflector -c 'Germany' -f 12 -l 10 -n 12 --save /etc/pacman.d/mirrorlist
```

## Install the base system

```sh
pacstrap /mnt base base-devel linux linux-firmware neovim man-db
```

Replace `linux` with `linux-lts` if you want to use the LTS-kernel.  

## Install additional packages and a bootloader

```sh
pacman -S networkmanager grub intel-ucode efibootmgr gvfs udisks2 os-prober dosfstools ntfs-3g
systemctl enable NetworkManager.service
```

If you use BIOS instead of UEFI, you don't need the `efibootmgr` package. The last three packages are only relevant if you want to dual boot Windows. Replace `intel-ucode` with `amd-ucode` if you have an AMD processor.

### Prepare Grub for LUKS

This only applies when the root partition is encrypted with LUKS.  
Before installing grub, add the following to `/etc/default/grub`
```sh
GRUB_CMDLINE_LINUX=”cryptdevice=/dev/sda3:luks_root”
GRUB_ENABLE_CRYPTODISK=y
```

Then, add the `encrypt`-keyword to the following line in `/etc/mkinitcpio.conf`

```sh
HOOKS=(base udev autodetect modconf block encrypt filesystems keyboard fsck)
```

Afterwards, run `mkinitcpio -p linux`. Now you can install GRUB.

### Install GRUB - UEFI

Create the directory where you want to mount the EFI partition.

```sh
mkdir /boot/efi
mount /dev/sda1 /boot/efi
```

Then install grub like this:

```sh
grub-install --target=x86_64-efi --bootloader-id=GRUB --efi-directory=/boot/efi --recheck
grub-mkconfig -o /boot/grub/grub.cfg
```

Edit the `/etc/default/grub` file.

```sh
DEFAULT_TIMEOUT=15
GRUB_CMDLINE_LINUX_DEFAULT="loglvel=3 quiet acpi_backlight=vendor"
```
Finish the installation, exit `chroot`, unmount your partitions and reboot.

# After the Installation

## Add a user

After a plain Arch install there is only the root user. Since you don't want to be logged in as root all the time, we need to add another regular user.

Edit the `sudoers`-file at `/etc/sudoers` with neovim.

```sh
sudo EDITOR=nvim visudo
```

Uncomment the line that says `%wheel ALL=(ALL) ALL`.  
Then, we add a new user called `simon` to the `wheel` group we just uncommented.

```sh
useradd -m -G wheel -s /bin/zsh simon
```

As a last step, the user needs a password:

```sh
passwd simon
```

## Install Yay

[Yay](https://github.com/Jguer/yay) is an AUR wrpaper, that enables you to install and maintain packages from the AUR.

```sh
pacman -S --needed git base-devel
git clone https://aur.archlinux.org/yay-bin.git
cd yay-bin
makepkg -si
```

## Install and enable some useful services

Some services are really usefull for resource and disk management.

```sh
sudo pacman -S acpid ntp dbus avahi cups
```

Then, enable these services, so they get automatically started at boot.

```sh
systemctl enable acpid
systemctl enable ntpd
systemctl enable avahi-daemon
systemctl enable org.cups.cupsd.service
```

## Firewall

```sh
sudo pacman -S ufw
```

Start and enable the `ufw.service` a minimalistic config can look like this:

```sh
sudo ufw default deny
sudo ufw limit ssh

sudo ufw enable     # run once after installing ufw
sudo ufw status     # verify
```

## Install sound server

```sh
sudo pacman -S alsa-utils pipewire pipewire-alsa wireplumber
```

This installs the pipewire sound server with the wireplumber client. Restart for changes to apply.

## Install a desktop environment

Install wayland and a compositor that can make use of the wayland protocol and some useful services.

```sh
sudo pacman -S wayland polkit waybar mako wl-clipboard xdg-desktop-portal, xdg-desktop-portal-wlr mesa
```

```sh
yay -S river rofi-lbonn-wayland wdisplays
```

### Phone support

Enables file browser support for Android phones (reboot for changes to take effect).
```sh
pacman -S mtpfs gvfs-mtp
yay -S jmtpfs
```

### LATEX

```sh
pac -S texlive-most     # choose which packages to install
pac -S biber perl-clone
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
