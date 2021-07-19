---
title: WordPress CLI
description: Install and configure WordPress CLI commands to run from anywhere.
tags: wordpress
---

```
if type "wp" > /dev/null; then
echo "WordPress CLI already installed"
else 
curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar
chmod +x wp-cli.phar
sudo mv wp-cli.phar /usr/local/bin/wp
echo "WordPress CLI has been successfully installed"
fi
```
