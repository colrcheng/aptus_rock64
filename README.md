# aptus_rock64
## Aptus Rock64 Development

https://github.com/ayufan-rock64/linux-build/releases/download/0.9.14/stretch-minimal-rock64-0.9.14-1159-arm64.img.xz

### Wifi Module


### Install packages
```console
sudo apt install phpmyadmin
sudo apt install shellinabox
sudo apt install iftop
sudo apt install htop
```
### Configurations
#### hostapd.conf
```bash
interface =wlan0
driver=nl80211
ctrl_interface=/var/run/hostapd
ctrl_interface_group=0
hw_mode=g
country_code=US
channel=11
auth_algs=1
wpa=0
macaddr_acl=0
disassoc_low_ack=0
#ieee80211n=1
#wmm_enabled=1
#ht_capab=[HT40-][SHORT-GI-20][SHORT-GI-40]
```
#### dhcpd.conf
```bash
static ip_address=192.168.169.2
static routers=192.168.169.2
```
#### dnsmasq.conf
```bash
dhcp-range=192.168.169.10,192.168.169.250,2h
dhcp-option=3,192.168.169.2
```

### Install Jupter Notebook

#### Some dependencies:
```console 
sudo apt-get update
sudo apt-get install python3-dev python3-pip build-essential libzmq3-dev
sudo apt-get install python-zmq
sudo python3 -m pip install --upgrade pip
sudo pip3 install jupyter
```

#### Running jupyter notebook
```console 
jupyter notebook --generate-config
jupyter notebook --ip=0.0.0.0 --no-browser
```

#### Running on startup:
Edit: /etc/crontab at the bottom of the file
```bash
SHELL=/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
@reboot pi cd /home/pi/ && jupyter notebook --ip=0.0.0.0 --no-browser &
```
