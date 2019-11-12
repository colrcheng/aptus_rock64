# Aptus 2019 - Build on Rock64 Single Board Computer(SBC)
## Build Aptus image from scratch

### Download on this image and write it to a MicroSD card: 
Source: [Ayunfan Rock64 Stretch minimal 0.9.14](https://github.com/ayufan-rock64/linux-build/releases/download/0.9.14/stretch-minimal-rock64-0.9.14-1159-arm64.img.xz) (243MB)

#### Extract .xz files
```console
$ sudo apt-get install xz-utils
$ unxz file.xz
```

#### Write image to a microSD card
Source: https://www.raspberrypi.org/documentation/installation/installing-images/
* DD command
```console
$sudo dd if=/path/to/sdcard.img of=/dev/sd(a,b,c) bs=4096 status=progress
$sudo sync
```
* Etcher
Source: https://www.balena.io/etcher/
* win32diskimager
Source: https://sourceforge.net/projects/win32diskimager/

### Boot Aptus/Rock64 for the first time
#### Hardware 
* Aptus/Rock64 board
* A microSD card with Ayunfan Rock64 Strech minimal image
* A HDMI capable monitor and HDMI cable
* Wireless/Wired keyboard
* Power bank or AC Adapter
* [ROCK64 USB WIFI 802.11B/G/N (RTL8188EU)](https://store.pine64.org/?product=usb-wifi-802-11bgn-rtl8188eu-for-rock64)
#### Steps
1. Insert microSD card to Aptus/Rock64 board
1. Connect Aptus/Rock64 to the monitor, keyboard and USB wifi adapter.
1. Power on the Aptus/Rock64 by connecting it to the powerbank or AC adapter.
1. Change the default password for rock64 user account.
1. Expand the root partition to max available space on the microSD card. This should be done automatically. 

### Shrink the rootfs Partition

Reasons for shrinking the rootfs partition (partition 7):
* Resize the image for a smaller size microSD card. [SD cards may have less space than claimed](https://www.picstop.co.uk/news/why-do-memory-cards-have-less-space-than-advertised.html). 
* Take less stroage space and easy for sharing.
* Take less time to transfer over the network and much less time to write to SD cards.

References: 
* [resize_rootfs.h](https://github.com/ayufan-rock64/linux-package/blob/master/root/usr/local/sbin/resize_rootfs.sh)
* [Resize by sfdisk](http://karelzak.blogspot.com/2015/05/resize-by-sfdisk.html)
```console
$sudo sgdisk -e /dev/sd*(or mmcblk*)
$sudo echo ", -100M" | sudo sfdisk -N7 /dev/sd*(or mmcblk*)
$sudo partprobe /dev/sd*(or mmcblk*)
$sudo resize2fs /dev/sd*p7(or mmcblk*p7)
```

### WiFi hotspot
#### Disable predictable network interface naming
References: 
* https://askubuntu.com/questions/826325/how-to-revert-usb-wifi-interface-name-from-wlxxxxxxxxxxxxx-to-wlanx
* https://askubuntu.com/questions/628217/use-of-predictable-network-interface-names-with-alternate-kernels
* https://www.itzgeek.com/how-tos/mini-howtos/change-default-network-name-ens33-to-old-eth0-on-ubuntu-16-04.html

```console
$sudo ln -s /dev/null /etc/udev/rules.d/80-net-setup-link.rules
$sudo reboot
$sudo ifconfig
```

#### Update the system
```console
$sudo apt update
$sudo apt upgrade
```

#### WiFi hotspot

##### Install WiFi configuration portal
Source: https://github.com/billz/raspap-webgui
```console
$wget -q https://git.io/voEUQ -O /tmp/raspap && bash /tmp/raspap
```

##### Disable wpa_supplicant
References:
* https://www.raspberrypi.org/forums/viewtopic.php?t=212574
* https://www.centos.org/forums/viewtopic.php?t=51125
```console
$sudo systemctl mask wpa_supplicant.service
```
##### Update hostapd.conf
```bash
interface =wlan0
driver=nl80211
ctrl_interface=/var/run/hostapd
ctrl_interface_group=0
hw_mode=g
country_code=US
channel=11
auth_algs=1
ssid=AptusRock64
wpa=0
macaddr_acl=0
disassoc_low_ack=0
#ieee80211n=1
#wmm_enabled=1
#ht_capab=[HT40-][SHORT-GI-20][SHORT-GI-40]
```
##### Update dhcpd.conf
```bash
static ip_address=192.168.169.2/24
static routers=192.168.169.2
```
##### Update dnsmasq.conf
```bash
dhcp-range=192.168.169.10,192.168.169.250,255.255.255.0,2h
dhcp-option=3,192.168.169.2
```


### Install packages
```console
sudo apt install phpmyadmin
sudo apt install shellinabox
sudo apt install iftop
sudo apt install htop
```


