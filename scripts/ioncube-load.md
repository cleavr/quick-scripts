---
title: IonCube Loader
description: IonCube Loader handles the reading and execution of encoded files at run time. This script will install for PHP 7.2 - 7.4. You'll like get a false error after script is ran - but check the output as IonCube Loader will likely actually be installed. 
runAs: root
tags: php
---


```
phpVersion={{ phpVersion }}

if [ -f /usr/local/ioncube_loaders*.tar.gz ]; then
cd /usr/local
sudo rm ioncube_loaders*.tar.gz
fi

cd /usr/local
sudo wget https://downloads.ioncube.com/loader_downloads/ioncube_loaders_lin_x86-64.tar.gz
sudo tar xzf ioncube_loaders_lin_x86-64.tar.gz

if [ $phpVersion = 7.4 ]; then
sudo cp ioncube/ioncube_loader_lin_7.4.so /usr/lib/php/20190902
sudo bash -c 'echo "zend_extension=/usr/lib/php/20190902/ioncube_loader_lin_7.4.so" > /etc/php/7.4/cli/conf.d/00-20ioncube.ini'
sudo service php7.4-fpm restart
fi

if [ $phpVersion = 7.3 ]; then
sudo cp ioncube/ioncube_loader_lin_7.3.so /usr/lib/php/20180731
sudo bash -c 'echo "zend_extension=/usr/lib/php/20180731/ioncube_loader_lin_7.3.so" > /etc/php/7.3/cli/conf.d/00-20ioncube.ini'
sudo service php7.3-fpm restart
fi

if [ $phpVersion = 7.2 ]; then
sudo cp ioncube/ioncube_loader_lin_7.2.so /usr/lib/php/20170718
sudo bash -c 'echo "zend_extension=/usr/lib/php/20170718/ioncube_loader_lin_7.2.so" > /etc/php/7.2/cli/conf.d/00-20ioncube.ini'
sudo service php7.2-fpm restart
fi

sudo systemctl restart nginx
```
