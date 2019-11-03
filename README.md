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
