# Arch Linux Installation Guide

> [!IMPORTANT]
> Always refer to the [Arch Install Guide](https://wiki.archlinux.org/index.php/installation_guide).

This document assues your device is using UEFI and that the bootable disk is called `/dev/nvme0n1`.

## Preparations

Frist, create a bootable USB from the Arch ISO (assuming the signature could be verified successfully).

> [!Important]
> Always specify the __full__ disk, __not__ the partition!

```sh
sudo dd bs=4M if=archlinux-2026.10.01-x86_64.iso of=/dev/sda status=progress oflag=sync
```

## Installing the System

### Verify the Boot Mode

If this command does not return `64`, you are on a BIOS system and this document does not apply.
```sh
cat /sys/firmware/efi/fw_platform_size
```

### Connect to the Internet

Ensure your network interface is listed.
```sh
ip link
```

#### Connect via Ethernet

DHCP is enabled by default, if you require a static IP you need to set it manually.
```sh
ip address add 192.168.1.10/24 dev eth0
```

#### Connect via WIFI

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

Afterwards, you should be connected to the internet, check this with `ping`.

If you still have no connection, check the DNS resolve configuration at `/etc/resolv.conf`.
Try adding the DNS server of your router or a public DNS server address.
```sh
nameserver 192.168.178.1
nameserver 1.1.1.1
```

Once you are connected to the Internet, sync the system clock.
```sh
timedatectl
```

## Prepare the Disk

For UEFI, you need to create a separate EFI partition.
The partition layout is as follows:

| Number | Path | Size | Type
| --- | --- | --- | --- |
| 1 | `/dev/nvme0n1p1` | 4G | `EFI System` |
| 2 | `/dev/nvme0n1p2` | Remainder of disk | `Linux root (x86)` |

Leave enough room on your EFI partition, in case you want to load additional kernels later.
Partition the disks with `fdisk /dev/nvme0n1`.
Create an empty `GPT` table and set the partition types as listed in the table above.

### Encrypting the Root Partition

If you want to encrypt the root partition, do it now.
```sh
cryptsetup luksFormat /dev/nvme0n1p2
```
You need to confirm this command with `YES`.

Open the encrypted container and make it available at `/dev/mapper/cryptlvm`.
```sh
cryptsetup open /dev/nvme0n1p2 cryptlvm
```

#### Create the LVM

Create the physical volume and the volume group on top of the LUKS container.
`vg` is the name of the volume group, you can choose any name here.
```sh
pvcreate /dev/mapper/cryptlvm
vgcreate vg /dev/mapper/cryptlvm
```

Create the logical volumes inside the volume group.
The swap volume should have the same size as your RAM, the root partition should be at least 32G.
```sh
lvcreate -L 40G vg -n swap
lvcreate -L 128G vg -n root
lvcreate -l 100%FREE vg -n home
```

> [!NOTE]
> `-L` by default reads in Megabytes. If you create a 128M root by mistake, `lvremove /dev/vg/root` and try again.

Now, create the file systems for all volumes.
```sh
mkfs.ext4 /dev/vg/root
mkfs.ext4 /dev/vg/home
mkswap /dev/vg/swap
```

Finally, mount all partitions.
```sh
mount /dev/vg/root /mnt
mount --mkdir /dev/vg/home /mnt/home
swapon /dev/vg/swap
```

### Setup Without Disk Encryption

```sh
mkfs.ext4 /dev/nvme0n1p2
mount /dev/nvme0n1p2 /mnt
```

### Prepare the EFI Partition

Independent of whether you setup disk encryption, format and mount the EFI partition.
```sh
mkfs.fat -F 32 /dev/nvme0n1p1
mount --mkdir /dev/nvme0n1p1 /mnt/efi
```

## Install the Base System

Replace `amd-ucode` with `intel-ucode` if you have an Intel processor.
```sh
pacstrap -K /mnt base base-devel linux linux-firmware mkinitcpio neovim git sudo lvm2 man-db man-pages amd-ucode networkmanager
```

## Configure the System

Make sure the entries in `/mnt/etc/fstab` are correct.
```sh
genfstab -U /mnt >> /mnt/etc/fstab
```

Afterwards, you can chroot into the new system.
```sh
arch-chroot -S /mnt
```

```sh
ln -sf /usr/share/zoneinfo/Europe/Berlin /etc/localtime
hwclock --systohc
```

Uncomment `en_US.UTF-8 UTF-8` in `/etc/locale.gen` and generate locale.
```sh
locale-gen
echo "LANG=en_US.UTF-8" > /etc/locale.conf
echo "arch" > /etc/hostname
```

Don't forget to enable networkmanager, so you have networking after you reboot.
```sh
systemctl enable NetworkManager.service
```

Set the root password
```sh
passwd
```

## Configure the Unified Kernel Image

> [!NOTE]
> This is not necessary if you did not setup disk encryption.

Open `/etc/mkinitcpio.conf` and edit the `HOOKS` line.
- `encrypt` must come _before_ `lvm2`
- `lvm2` must come _before_ `filesystems`.
- `keyboard` and `keymap` must come _before_ `encrypt`

```
HOOKS=(base systemd autodetect microcode modconf kms keyboard keymap sd-vconsole block sd-encrypt lvm2 filesystems fsck)
```

`mkinitcpio` reads from `/etc/cmdline.d/`, so we need to store the partition UUID there.
You need to make sure the output is correct, otherwise the system cannot boot.
```sh
mkdir -p /etc/cmdline.d
echo "rd.luks.name=$(blkid -s UUID -o value /dev/nvme0n1p2)=cryptlvm root=/dev/vg/root rw" > /etc/cmdline.d/root.conf
```

Edit `/etc/mkinitcpio.d/linux.preset` so it contains the following:
```
ALL_kver="/boot/vmlinuz-linux"

PRESETS=('default' 'fallback')

#default_image="/boot/initramfs-linux.img"
default_uki="/efi/EFI/Linux/arch-linux.efi"
default_options="--splash /usr/share/systemd/bootctl/splash-arch.bmp"

#fallback_image="/boot/initramfs-linux-fallback.img"
fallback_uki="/efi/EFI/Linux/arch-linux-fallback.efi"
fallback_options="-S autodetect"
```

Last, build the image.
```sh
mkdir -p /efi/EFI/Linux
mkinitcpio -P
ls /efi/EFI/Linux
rm /boot/initramfs-linux.img
```

`ls` must return `arch-linux.efi` and `arch-linux-fallback.efi`.


## systemd-boot

Install the bootloader with:
```sh
bootctl install
```

Make sure the following files are present:
```
EFI/
    BOOT/
        BOOTX64.EFI
    systemd/
        systemd-bootx64.efi
    amd-ucode.img
    initramfs-linux.img
    loader/
    vmlinuz-linux.img
```

Edit `/boot/EFI/loader/loader.conf` and add the following:
```conf
default arch.conf
timeout 5
console-mode max
editor no
```

Make sure to keep the boot loader updates
```sh
systemctl enable systemd-boot-update.service
```

systemd-boot likely showed the warning `random seed file is world accessible` during installation, this can be fixed by setting `fmask=0077` and `dmask=0077` in `/etc/fstab` for the `/efi` partition entry.

Notice that we declare an `arch.conf` file, which holds the boot configuration.
Create `arch.conf` in `/boot/loader/entries/arch.conf`.
First, obtain the UUID of the root partition via `lsblk -dno UUID /dev/partition` (`blkid -s UUID -o value /dev/partition` works as well).
As you cannot copy-paste in the Arch live environment, use the following snippet to copy the disk UUID into the config file.

```sh
echo $(lsblk -dno UUID /dev/partition) > /boot/loader/entries/arch.conf
```

The `arch.conf` looks as follows. If you use Intel or AMD ucode, make sure to specify this image _before_ the `initramfs-linux` image.
```conf
title Arch Linux
linux /vmlinuz-linux
initrd /amd-ucode.img
initrd /initramfs-linux.img
options root=UUID=<DISK_UUID> rm
```

Also consider installing the `linux-lts` kernel as a fallback, which gets loaded by `arch-lts.conf`.
```conf
title Arch Linux LTS
linux /vmlinuz-linux-lts
initrd /amd-ucode.img
initrd /initramfs-linux-lts.img
options root=UUID=<DISK_UUID> rm
```

If you setup disk encryption, make sure to check and adjust the paths to the kernel images.
Check if systemd-boot is happy with `bootctl status`.


### Enable Weekly TRIM for SSDs

> [!WARNING]
> By default, TRIM is disabled for LUKS devices, as it can lead to imformation leakage.

```sh
cryptsetup --allow-discards --persistent refresh cryptlvm
```

Regardless of disk encryption, enable `fstrim.timer`, which runs weekly by default.
```sh
pacman -Syu util-linux
systemctl enable fstrim.timer
```

## Complete the Installation

```sh
exit
umount -R /mnt
reboot
```


# After the Installation

## Register the Boot Entry

Login as `root`.
```sh
bootctl install
```

## Backup the LUKS Header

The first few MB of the encrypted partition are the LUKS header.
If it ever gets damaged, the data is lost forever.
```sh
cryptsetup luksHeaderBackup /dev/nvme0n1p2 --header-backup-file luks-header.img
# restoring the header
cryptsetup luksHeaderRestore /dev/nvme0n1p2 --header-backup-file luks-header.img
```

## Sign the Bootloader

We use the `sbctl` utility to sign the bootloader and enroll the keys.
```sh
pacman -Syu sbctl
systemctl reboot --firmware-setup
```

Once in the firmware settings, navigate to the secure boot options and delete the existing keys (often called `Clear Secure Boot keys` or `Reset to Setup Mode`).
Also make sure TPM is enabled.

### Create and Enroll the Keys

This should report `Setup Mode: ✗ Enabled` and `Secure Boot: ✗ Disabled`.
```sh
sbctl status
```

```sh
sbctl create-keys
sbctl enroll-keys
```

The created keys are stored in `/var/lib/sbctl/keys`.
If your device requires Microsoft certificates to boot, enroll it as well.
```sh
sbctl enroll-keys -m
```

Sign the bootloader and kernel images.
```sh
sbctl sign -s /efi/EFI/systemd/systemd-bootx64.efi
sbctl sign -s /efi/EFI/BOOT/BOOTX64.EFI
sbctl sign -s /efi/EFI/Linux/arch-linux.efi
sbctl sign -s /efi/EFI/Linux/arch-linux-fallback.efi
sbctl sign -s -o /usr/lib/systemd/boot/efi/systemd-bootx64.efi.signed /usr/lib/systemd/boot/efi/systemd-bootx64.efi
sbctl verify
```

`verify` must list all files as singed.
Reboot again and enable secure boot.
```sh
systemctl reboot --firmware-setup
```

Afterwards, check with `sbctl status` that secure boot is _enabled_ and setup mode is _disabled_.
Pacman automatically signs new images, check that the output of `sbctl verify` shows that all files have been signed.
If you ever reinstall the bootloader with `bootctl install`, you also need to sign it again with `sbctl sign-all`, otherwise the system won't boot.


## Add a User

Edit the `sudoers`-file at `/etc/sudoers` with neovim.
```sh
sudo EDITOR=nvim visudo
```
Uncomment the line `%wheel ALL=(ALL) ALL`.

Create a new user called `simon` and add it to the `wheel` group we just uncommented.
```sh
useradd -m -G wheel -s /bin/zsh simon
```

As a last step, the user needs a password:
```sh
passwd simon
```
From now on, you use the system as you normal user, not as root.

## Install an AUR Helper

[Paru](https://github.com/morganamilo/paru) is an AUR wrpaper, that enables you to install and maintain packages from the AUR.

```sh
pacman -Syu --needed git base-devel
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si
```

## Install a Sound Server

```sh
sudo pacman -Syu pipewire wireplumber pipewire-alsa alsa-utils pipewire-audio pipewire-jack pipewire-pulse wiremix
```
This installs the pipewire sound server with the wireplumber client. Restart for changes to apply.

Install and auto-start mpd and friends
```sh
sudo pacman -Syu mpd mpc mpd-mpris playerctl ncmcpcpp
systemctl --user enable --now mpd.service
systemctl --user enable --now mpd-mpris.service
# make sure to disable avahi
systemctl disable --now avahi-daemon.service
```

### Enable Mono Audio for Pipewire

The setting gets saved in `~/.local/state/wireplumber`.
```sh
wpctl settings --save node.features.audio.mono true
```

## Install a Wayland Compositor

```sh
sudo pacman -Syu mangowm xdg-desktop-portal xdg-desktop-portal-gtk xdg-desktop-portal-wlr
```

## Bluetooth

Install the following packages.
The blueman applet gets started by river.
```sh
pacman -S --needed bluez bluez-libs bluez-utils blueman
systemctl enable --now bluetooth.service
```

To autostart the bluetooth daemon while keeping bluetooth powered off:
- Edit `/etc/bluetooth/main.conf` and set `AutoEnable=false` in the `[Policy]` section.
- Restart the daemon with `systemdctl restart bluetooth.service`

### Phone Support

Enables file browser support for Android phones.
```sh
pacman -Syu android-udev mtpfs gvfs-mtp
```
