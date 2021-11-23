
# Laptop installation guide

This guide describes how to install Arch Linux on a Huawei Matebook 13 (2020) and enabling most of its features.

# Before the Installation

If Windows is already installed, leave it that way. You just need to partition to disk accordingly to your needs. Shrink the Windows partition using Windows, and create new partitions using `fdisk`. Also Secure Boot needs to be disabled in the BIOS. Also Hibernation and Fast Startup need to be disabled in Windows 10. Windows does create an EFI partition upon installation, so you don't have to create on of your own. Then you can start the Arch installation process following the Installation Guide from the Wiki.

# Installation Process

Follow the instructions from the Installation Guide. As connecting to wifi can be a real pain in Arch, I recommend using your phone as a USB thetering hotspot.
When you have completed the installation process, you need some additional packages to enable the dualboot without hickups.

```bash
pacman -S grub efibootmgr os-prober dosfstools gvfs ntfs-3g udisks2 amd-ucode
```

Then, create the efi-directory.

```bash
mkdir /boot/efi
mount /dev/nvme0n1p1 /boot/efi
```

Now, you need to edit the `/etc/default/grub` file.
Set `DEFAULT_TIMEOUT=15` and `GRUB_CMDLINE_LINUX_DEFAULT="loglvel=3 quiet acpi_backlight=vendor"`.

```bash
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=grub --recheck
grub-mkconfig -o /boot/grub/grub.cfg
```

Due to the package os-prober, grub recognizes Windows 10 automatically and adds the corresponding entry in the grub-menu.
Finally, install some last packages:

```bash
pacman -S iw wpa_supplicant dialog networkmanager
```

Then exit `arch-chroot`, umount the partition and reboot.



# Post installation

Install the needed packages following the `after-arch-install.md`. Also copy config files etc., so you have a working basic system.

## Manage battery and performance

Install the `thermald`, `tlp`, `cpupower` and `turbostat` packages.

Then, enable their respective services.

```bash
sudo systemctl enable thermald.service
sudo systemctl enable tlp.service
sudo systemctl enable cpupower.service
```

## Screen lock on lid close

To lock the device to sleep before it goes to sleep, create the file `/etc/systemd/lock.service`.

```bash
[Unit]
Description=Lock the screen on resume

[Service]
User=simon
Environment=DISPLAY=:0
ExecStart=/usr/bin/slock

[Install]
WantedBy=suspend.target sleep.target hibernate.target hybrid-sleep.target suspend-then-hibernate.target
```
Then enable `sudo systemctl enable lock.service.`

## Touchpad configuration

Create a file at `/etc/X11/xorg.conf.d/30-touchpad.conf`.

```bash
Section "InputClass"
	Identifier "libinput touchpad catchall"
	MatchIsTouchpad "on"
	MatchDevicePath /dev/input/event*"
	Option "Tapping" "on"
	Option "TapButton1" "1"
	Option "TapButton2" "3"
	Option "TapButton3" "2"
	Option "TappingDrag" "True"
	Option "Accelprofile" "linear"
	Option "AccelSpeed" "0.4"
	Option "DisableWhileTyping" "True"
	Option "VertTwoFingerScroll" "True"
	Option "HorizTwoFingerScroll" "True"
	Option "NaturalScrolling" "True"
	Driver "libinput"
EndSection
```

## Controlling the keyboard backlight

Since `xbacklight` does not work, the `light` package is needed.
In `rc.lua` replace the xbacklight commands with light:

```lua
awful.key({ }, "XF86MonBrightnessUp", function () os.execute("light -A 7") end,
	  {description = "+10%", group = "hotkeys"}),
awful.key({ }, "XF86MonBrightnessDown", function () os.execute("light -U 7") end,
	  {description = "-10%", group = "hotkeys"}),
```

## Remap keys

Install the `xorg-xkeymaps` package.
Export the default keymap to a file:

```bash
xmodmap -pke > `/.Xmodmap
```

Locate the key you want to change and replace its value with the new one.
For example:

```bash
# this remaps the key with the greater/smaller than keys on the german keyboard with shift
keycode 94 = Shift_L NoSymbol Shift_L
```

Alternatively, you can test settings temporarily with:

```bash
xmodmap -e "94 = Shift_L NoSymbol Shift_L"

```
