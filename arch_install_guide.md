# Arch Linux installation guide

Always refer to the [Arch Wiki](https://wiki.archlinux.org/index.php/installation_guide).  
This document shows only a few key commands that might not be in the Arch Installation Guide. This is not complete.

## Connect to the Internet

Ensure your network interface is listed:

```sh
ip link
```

### Connect via ethernet (recommended)

DHCP for ethernet connections is enabled by default. So just plug in the ethernet cable.  
If you are using a connection which requires a static IP you can add it with the `add` command. You need to state the IP adress and the netmask.

```sh
ip address add 192.168.1.10/24 dev eth0
```

Same applies to wireless interfaces (like wlan0)

### Connect via WIFI

If your wireless connection requires a static IP, follow the steps above, they are the same as for an ethernet connection.  
To scan and connect to a wireless network, use the `wifi-menu`-command and follow the on-screen instructions.  
If this does not work, you need to connect to teh WIFI manually.  
First, list all wireless devices.

```sh
iwctl device list
```

We assume, your wifi interface is `wlan0`. If you have another interface, just exchange the name.  
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
If you still have no connection, check the name resolve configuration.

```sh
cat /etc/resolv.conf
```

Add the DNS server of your router or a public DNS server address.

```sh
nameserver 192.168.1.1
nameserver 0.0.0.0
```

## Create and format the partitions

If you use BIOS, one root partition is enough, if you use UEFI, don't forget to create an EFI-partition first.

# Install the Arch system

## Select the mirrors

Arch has a huge number of mirrors, but it might not prefet the fastest ones. If a slower mirror is chosen, the downloads of the system will take significantly longer.  
First, sync the pacman repository.

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

Install the base system with `pacstrap`.

```sh
pacstrap /mnt base base-devel linux linux-firmware vim man-db
```

Replace `linux` with `linux-lts` if you want to use the LTS-kernel.  

# Install additional packages

After you finished installing and configuring your system, there are a few more useful steps to take.

```sh
pacman -S networkmanager grub efibootmgr intel-ucode
```

If you use BIOS instead of UEFI, you don't need the `efibootmgr` package.
Replace `intel-ucode` with `amd-ucode` if you have an AMD processor.

## Install GRUB - BIOS

Install GRUB to the corresponding device (use the whole device, not a partition):

```sh
grub-install /dev/sda
```

## Install GRUB - UEFI

Create the directory, where you want to mount the EFI partition.

```sh
mkdir /boot/efi
```

Now, mount your EFI partition.

```sh
mount /dev/sda1 /boot/efi/
```

Then install grub like this:

```sh
grub-install --target=x86_64-efi --bootloader-id=GRUB --efi-directory=/boot/efi
```

As the last step, write the GRUB config (applies to both BIOS and UEFI):

```sh
grub-mkconfig -o /boot/grub/grub.cfg
```

Also, enable the NetworkManager-service.

```sh
systemctl enable NetworkManager.service
```

Now you can exit `chroot`, unmount your partitions and reboot.
