# aptus_rock64
## Aptus Rock64 Development

### Wifi Module


### Packages installed
``apt install phpmyadmin``


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
