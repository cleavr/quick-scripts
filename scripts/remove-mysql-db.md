---
title: Purge MySQL from server
description: This will purge MySQL from a server. You should backup your databases before performing this if you wish to hold on to your data.  
runAs: root
tags: database, mysql
---

```
sudo apt-get purge mysql-server mysql-client
sudo apt-get autoremove 
sudo apt-get autoclean
```
