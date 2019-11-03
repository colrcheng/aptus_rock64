# aptus_rock64
## Aptus rock64 development

### Wifi Module


### Packages installed
``apt install phpmyadmin``


### Configurations


### Install Jupter Notebook

#### Some dependencies:
``sudo apt-get update ``

``sudo apt-get install python3-dev python3-pip build-essential libzmq3-dev``

``sudo apt-get install python-zmq``
``sudo python3 -m pip install --upgrade pip``
``sudo pip3 install jupyter``

#### Running jupyter notebook
``
jupyter notebook --generate-config;
jupyter notebook --ip=0.0.0.0 --no-browser; # now it accessable on the whole network;
``
#### Running on startup:
Edit: /etc/crontab at the bottom of the file (after the last '#'):
``
SHELL=/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/games:/usr/games
@reboot pi cd /home/pi/ && jupyter notebook --ip=0.0.0.0 --no-browser &
``
