---
title: Purge MariaDB from server
description: This will purge MariaDB from a server. You should backup your databases before performing this if you wish to hold on to your data.  
runAs: root
tags: database, maria
---

```
sudo apt-get purge mariadb-server
sudo apt-get autoremove 
sudo apt-get autoclean
```
