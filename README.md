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

#### Backup image: AptusRock6420191108V01.img

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
#### Backup image: AptusRock6420191108V02.img 

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

#### WiFi portal/webUI

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
##### Backup image: AptusRock6420191111V03.img
##### Update lighttpd.conf
```bash
server.document-root="/var/www/webui"
server.port=81
```
* Move the webUI files to /var/www/webui
* Change the default password for WebUI.

### LAMP 
References: 
* https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mariadb-php-lamp-stack-debian9
* https://www.digitalocean.com/community/tutorials/how-to-rewrite-urls-with-mod_rewrite-for-apache-on-ubuntu-16-04
* https://tecadmin.net/install-php-debian-9-stretch/
* https://medium.com/andrewmmc-io/upgrade-php-version-to-7-2-from-7-0-c005a0926642

#### Apache2
```console
$sudo apt install apache2
$sudo a2enmod rewrite
$sudo a2dismod reqtimeout
$sudo systemctl restart apache2
```
/etc/apache2/sites-available/000-default.conf
```bash
<VirtualHost *:80>
    <Directory /var/www/html>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All
        Require all granted
    </Directory>

    . . .
</VirtualHost>
```
#### MySQL/MariaDB
```console
$sudo apt install mariadb-server
$sudo mysql_secure_installation
```
#### PHP 7.3
```console
$sudo apt install ca-certificates apt-transport-https 
$wget -q https://packages.sury.org/php/apt.gpg -O- | sudo apt-key add -
$echo "deb https://packages.sury.org/php/ stretch main" | sudo tee /etc/apt/sources.list.d/php.list
$sudo apt update
$sudo apt install php7.3
$sudo apt install graphviz aspell ghostscript clamav php7.3-pspell php7.3-curl php7.3-gd php7.3-intl php7.3-mysql php7.3-xml php7.3-xmlrpc php7.3-ldap php7.3-zip php7.3-soap php7.3-mbstring
$sudo apt install sqlite3 php7.3-sqlite3
```
/etc/apache2/mods-enabled/dir.conf
```bash
<IfModule mod_dir.c>
    DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```
/etc/php/7.3/apache2/php.ini
```bash
post_max_size = 2000M
upload_max_size = 2000M
max_input_time = 3600
max_execution_time = 3600
memory_limit = 256M
```

### Install packages
```console
sudo apt install phpmyadmin
sudo apt install shellinabox
sudo apt install iftop
sudo apt install htop
```

### WordPress
Reference: https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-with-lamp-on-debian-9
# Download latest WordPress
```console
$wget https://wordpress.org/latest.tar.gz
$tar -xzvf latest.tar.gz
$sudo mv wordpress/* /var/www/html
```
* Create database and db user.


### Moodle
Reference: https://docs.moodle.org/37/en/Step-by-step_Installation_Guide_for_Ubuntu

### ownCloud
Reference: https://linuxhostsupport.com/blog/how-to-install-owncloud-10-on-debian-9/

### Kiwix
References:
* https://wiki.kiwix.org/wiki/Kiwix-manage
* https://wiki.kiwix.org/wiki/Kiwix-serve
* https://download.kiwix.org/zim/




