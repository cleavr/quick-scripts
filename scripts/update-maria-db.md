---
title: Update MariaDB Version
description: Script that updates MariaDB to the desired version. Keep in mind that updating is only possible to a higher version than the currently installed one. This is an unofficial script, that can cause unexpected behaviors, so run it at your own risk. After running, it may say error, but it is most likely only a warning because a source for MariaDB already existed. In case anything really fails, a backup of your DB's are at /var/lib/mysql_backup
runAs: root
tags: mariadb
---

```
curl -LsS -O https://downloads.mariadb.com/MariaDB/mariadb_repo_setup
bash mariadb_repo_setup --mariadb-server-version={{MARIADB_VERSION}}
apt-get update
cp -v -a /var/lib/mysql/ /var/lib/mysql_backup
apt-get install mariadb-server -y
systemctl daemon-reload
systemctl start mariadb.service
mysql_upgrade -uroot --force
rm -rf ./mariadb_repo_setup
```
