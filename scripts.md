title: "VIM Config"
description: "Set vim settings for command line."
tags: "vim"

```
TODO
```

title: "Cloudflare - Restore original visitor IPs"
description: "If you use Cloudflare and need to use the original requesting IP instead Cloudflare's proxy IP for a downstream process, run this script to pull the visitor's IP. This adds a lookup file that will be used by NGINX."
tags: "cloudflare"

```
#Verify IP list on https://support.cloudflare.com/hc/en-us/articles/200170786-Restoring-original-visitor-IPs-logging-visitor-IP-addresses

FILE=/etc/nginx/conf.d/cloudflare_restore_ips.conf
if test -f "$FILE"; then
    echo "File already exists."
    exit 0
fi

touch "$FILE"

echo "
set_real_ip_from 103.21.244.0/22;
set_real_ip_from 103.22.200.0/22;
set_real_ip_from 103.31.4.0/22;
set_real_ip_from 104.16.0.0/13;
set_real_ip_from 104.24.0.0/14;
set_real_ip_from 108.162.192.0/18;
set_real_ip_from 131.0.72.0/22;
set_real_ip_from 141.101.64.0/18;
set_real_ip_from 162.158.0.0/15;
set_real_ip_from 172.64.0.0/13;
set_real_ip_from 173.245.48.0/20;
set_real_ip_from 188.114.96.0/20;
set_real_ip_from 190.93.240.0/20;
set_real_ip_from 197.234.240.0/22;
set_real_ip_from 198.41.128.0/17;
set_real_ip_from 2400:cb00::/32;
set_real_ip_from 2606:4700::/32;
set_real_ip_from 2803:f800::/32;
set_real_ip_from 2405:b500::/32;
set_real_ip_from 2405:8100::/32;
set_real_ip_from 2c0f:f248::/32;
set_real_ip_from 2a06:98c0::/29;

#use any of the following two

real_ip_header CF-Connecting-IP;
#real_ip_header X-Forwarded-For;" >> "$FILE"

echo "Cloudflare restore original IPs file has been successfully added."
exit 0
```

title: "WordPress CLI"
description: "Install and configure WordPress CLI commands to run from anywhere."
tags: "wordpress"

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

title: ""
description: ""
tags: ""

```
```

title: ""
description: ""
tags: ""

```
```

title: ""
description: ""
tags: ""

```
```
