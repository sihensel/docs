# Laptop installation

This guide describes how to install Arch Linux on a Laptop and how to enable most features.

## Before the Installation

If Windows is already installed, leave it that way. You just need to partition to disk accordingly to your needs. Shrink the Windows partition and create new partitions using `fdisk`.  
Secure Boot needs to be disabled in the BIOS. Also Hibernation and Fast Startup need to be disabled in Windows 10.  
Windows does create an EFI partition upon installation, so you don't have to create on of your own.

## Useful drivers and other packages
Use the current kernel to make use of the latets WIFI drivers.
Install `sof-firmware` and `alsa-ucm-conf` sound driver packages and unmute all channels.

To use openvpn and the networkmanager-applet, install the `openvpn`, `nm-applet`, `nm-connection-editor` and `networkmanager-openvpn` packages.

## Manage battery and performance

Install the `tlp` and `auto-cpufreq` (AUR) packages and enable their services.

```sh
systemctl enable tlp.service
systemctl enable auto-cpufreq.service
```

## Screen lock on lid close

To lock the device to sleep before it goes to sleep, create the file `/etc/systemd/system/lock.service`.

```sh
[Unit]
Description=Lock the screen on resume

[Service]
User=simon
Environment=DISPLAY=:0
ExecStart=/usr/bin/slock

[Install]
WantedBy=suspend.target sleep.target hibernate.target hybrid-sleep.target suspend-then-hibernate.target
```
Then enable `systemctl enable lock.service.`

## Touchpad configuration

Create a file at `/etc/X11/xorg.conf.d/70-synaptics.conf`.

```sh
Section "InputClass"
	Identifier "touchpad catchall"
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
	Driver "synaptics"
EndSection
```

## Controlling the keyboard backlight

Copy the Python script from the [Arch Wiki](https://wiki.archlinux.org/title/Keyboard_backlight) and add the file to the path.

## Remap keys

Install the `xorg-xkeymaps` package.
Export the default keymap to a file:

```sh
xmodmap -pke > `/.Xmodmap
```

Locate the key you want to change and replace its value with the new one.
For example:

```sh
# this remaps the key with the greater/smaller than keys on the german keyboard with shift
keycode 94 = Shift_L NoSymbol Shift_L
```

Alternatively, you can test settings temporarily with:

```sh
xmodmap -e "94 = Shift_L NoSymbol Shift_L"
```
