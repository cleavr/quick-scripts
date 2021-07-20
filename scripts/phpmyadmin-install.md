---
title: PHPMyAdmin Install
description: PHPMyAdmin installation. This installation installs for production environments. 
runAs: cleavr
tags: php, wordpress
---

TODO: This needs more work. Fails at switching to MYSQL and also at removing directories for some reason... 

```
sudo add-apt-repository ppa:phpmyadmin/ppa
sudo apt-get update
sudo apt-get install phpmyadmin

cd phpmyadmin
rm –rf test setup

sudo mysql -u root -pee43b511c62f0fdf
CREATE USER '{{ dbUser }}'@'localhost' IDENTIFIED WITH caching_sha2_password BY '{{ dbPassword }}';
GRANT ALL PRIVILEGES ON *.* TO '{{ dbUser }}'@'localhost' WITH GRANT OPTION;
exit

mv phpmyadmin {{ sitePathToCurrent }}
```
