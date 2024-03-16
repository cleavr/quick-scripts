---
title: Deploy Laravel Sites
description: This script will copy the project from repo, links environment, activates deployment and cleans older deployment 
tags: deployment, laravel, sites
---

```
## Copy Project
echo 'Cloning {{ repository }}:{{ branch }} to {{ releasePath }}'
mkdir -p {{ releasePath }}

echo 'Linking {{ latestPath }} to {{ releasePath }}'
rm -f {{ latestPath }} > /dev/null 2>&1
ln -sfn "{{ releasePath }}" "{{ latestPath }}" > /dev/null 2>&1
chmod 775 {{ projectPath }}/releases
{{ cloneCommand }} {{ releasePath }} --quiet
if [ -d "{{ currentPath }}" ]; then
  # if it's not a link, then we'll just back it up
  if [ ! -L "{{ currentPath }}" ]; then
    echo "Found an existing {{ currentPath }} directory. Removing it."
    rm -rf "{{ currentPath }}" > /dev/null 2>&1
  fi
fi
chmod 0600 /home/{{ username }}/.ssh/id_rsa > /dev/null 2>&1

echo 'Checking the shared storage directory'
if [ -d "{{ projectPath }}/storage" ]; then
    echo 'Found shared and linked storage directory.'
else
    # if release path has storage folder then copy if not make a new one
    if [ -d "{{ appFolderPath }}/storage" ]; then
        echo "Found the shared storage directory. Moving storage path..."
        mv "{{ appFolderPath }}/storage" "{{ projectPath }}/"
    else
        mkdir -p "{{ projectPath }}/storage"
    fi
    chown -R {{ username }}:www-data "{{ projectPath }}/storage"
    chmod -R 775 "{{ projectPath }}/storage"
fi

# remove the storage folder if exists
if [ -d "{{ appFolderPath }}/storage" ]; then
    echo "Storage directory {{ appFolderPath }}/storage is pushed. To make data persist across deployments, removing this directory and linking it to {{ projectPath }}/storage instead."
    rm -rf "{{ appFolderPath }}/storage"
fi

ln -s "{{ projectPath }}/storage" "{{ appFolderPath }}/storage"
echo "Successfully linked {{ appFolderPath }}/storage to {{ projectPath }}/storage"

cd "{{ appFolderPath }}"
# link .env file if not already linked
echo 'Checking {{ projectPath }}/.env file'
if [ -f "{{ projectPath }}"/.env ]; then
    echo 'Found .env file in {{ projectPath }}'
else
  echo "Didn't find the .env file in {{ projectPath }}"
  # See if .env file is pushed, if so warn the user, use it as a starting template, and then move it to {{ sudoUsername }}.
  if [ -f "{{ appFolderPath }}"/.env ]; then
    echo '[WARNING!] It looks like you have pushed the .env file. This is *not* considered to be a good practice. We are using this as a starting template and renaming the original to {{ sudoUsername }}. {{ projectPath }}/.env is the one we will be using.'
    cp "{{ appFolderPath }}"/.env "{{ projectPath }}"/.env
    mv "{{ appFolderPath }}"/.env "{{ appFolderPath }}"/{{ sudoUsername }}
  else
    echo '{{ projectPath }}/.env file does not exist. Creating an empty one for linking.'

cat > {{ projectPath }}/.env << EOF

# This is a default env file = Be sure to update this file for your webapp
APP_NAME={{ domain }}

APP_ENV=local
APP_KEY=base64:$(env LC_CTYPE=C tr -dc "A-Za-z0-9_$%^&*()-+=" < /dev/urandom | head -c 32 | base64)
APP_DEBUG=false

APP_URL=http://{{ domain }}

LOG_CHANNEL=stack

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE={{ domain }}

DB_USERNAME=root
DB_PASSWORD=

BROADCAST_DRIVER=log
CACHE_DRIVER=file
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=smtp.mailtrap.io
MAIL_PORT=2525
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS=null
MAIL_FROM_NAME={{ sudoUsername }}

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
PUSHER_APP_CLUSTER=mt1

MIX_PUSHER_APP_KEY=
MIX_PUSHER_APP_CLUSTER=

EOF
fi
fi

if [[ ! -f "{{ releasePath }}"/.env && -f "{{ projectPath }}/.env" ]]; then
  chown {{ username }}:{{ username }} {{ projectPath }}/.env
  chmod 0600 {{ projectPath }}/.env
  ln -s "{{ projectPath }}/.env" "{{ releasePath }}/.env"
  echo "Successfully linked {{ releasePath }}/.env to {{ projectPath }}/.env"
else
  echo "Env path {{ releasePath }}/.env is already linked to {{ projectPath }}/.env"
fi

## Link Env
if [[ ! -f "{{ releasePath }}"/.env && -f "{{ projectPath }}/.env" ]]; then
  chown {{ username }}:{{ username }} {{ projectPath }}/.env
  chmod 0600 {{ projectPath }}/.env
  ln -s "{{ projectPath }}/.env" "{{ releasePath }}/.env"
  echo "Successfully linked {{ releasePath }}/.env to {{ projectPath }}/.env"
else
  echo "Env path {{ releasePath }}/.env is already linked to {{ projectPath }}/.env"
fi

## Installing composer
cd "{{ appFolderPath }}"
php{{ phpVersion }} `which composer` install --no-dev --no-ansi --no-interaction --optimize-autoloader --prefer-dist

## Installing yarn dependencies
cd "{{ appFolderPath }}"

if [ -f yarn.lock ]; then
  echo 'Found yarn.lock. Installing packages yarn with frozen lockfile'
  yarn install --frozen-lockfile
elif [ -f package-lock.json ]; then
  echo 'Found package-lock.json. Installing packages using npm ci'
  npm ci
else
  echo 'Missing lock file(s). Installing packages using npm install'
  npm install
fi

## Build Frontend Production Assets
cd "{{ appFolderPath }}"
echo "Building the app using {{{ buildCommand }}} ..."
npm run build


## Build Config Cache
ln -sfn "{{ appFolderPath }}" "{{ currentPath }}"
echo "Activated {{ appFolderPath }}"
cd "{{ appFolderPath }}"
php artisan config:cache


## Migrate database
ln -sfn "{{ appFolderPath }}" "{{ currentPath }}"
echo "Activated {{ appFolderPath }}"
echo 'Preparing to migrate the database'
if [ -f "{{ projectPath }}"/.env ]; then
    cd "{{ appFolderPath }}"
    php{{ phpVersion }} artisan migrate --force
else
  echo ".env file is needed to run the migration. Database migration is skipped in this deployment."
fi

## Activate new deployment
ln -sfn "{{ appFolderPath }}" "{{ currentPath }}"
echo "Activated {{ appFolderPath }}"
cd "{{ releasesPath }}"
echo 'Reloading PHP-FPM'
sudo service php{{ phpVersion }}-fpm reload

## Clean Old Deployments
cd "{{ releasesPath }}"
echo "Cleaning up {{ releasesToPrune }}"
rm -rf {{ releasesToPrune }} > /dev/null 2>&1
```
