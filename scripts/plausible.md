---
title: Plausible Analytics
description: Before running script, first install Docker in the server's services section and add a new Generic Port App to your server and set port to 8000. Then, run the script as cleavr user.  
runAs: root
tags: analytics
---

```
cd /home/cleavr
mkdir plausible
cd plausible
git clone https://github.com/plausible/hosting
cd hosting
key="$(openssl rand -base64 64 | tr -d '\n' ; echo)"
echo "
ADMIN_USER_EMAIL={{ADMIN_USER_EMAIL}}
ADMIN_USER_NAME={{ADMIN_USER_NAME}}
ADMIN_USER_PWD={{ADMIN_USER_PWD}}
BASE_URL={{BASE_URL}}
SECRET_KEY_BASE=$key
" > plausible-conf.env

docker-compose up -d
```
