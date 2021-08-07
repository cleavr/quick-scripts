---
title: Set WordPress file permissions
description: Sometimes you may need to reset file permissions or update en masse. This will assign directories (type d) with 755, files (type f) with 644, and wp-config with 640. Change default settings per your requirements. Read this article for additional info regarding WordPress folder and file permissions: https://wordpress.org/support/article/changing-file-permissions/   
runAs: root
tags: wordpress
---

```
user={{ serverUser }}
site={{ siteName }}
find /home/$user/$site/current -type d -exec chmod 755 {} \;
find /home/$user/$site/current -type f -exec chmod 644 {} \;
chmod 640 /home/$user/$site/current/wp-config.php
```
