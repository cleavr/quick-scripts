---
title: Clean old backup files from server
description: The script removes backup files older than the specified days. We provide `Clean Old Backups` feature for local backups from Cleavr itself. If your use case is similar you can create a cron job with following script to clear old backup files periodically. 
runAs: root
tags: backup
---

```
find destinationPath -name '*.tar.gz' -mtime +numberOfDays -type f -delete
```

`destinationPath` = this is the path to your backup files for e.g. /home/cleavr/backups
`numberOfDays` = files older than `numberOfDays` will be cleared. To clear file
