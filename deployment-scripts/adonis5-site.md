---
title: Deploy Adonis5 Sites
description: This script will copy the project from repo, links environment, build app, activates deployment and cleans older deployment 
tags: deployment, adonis, node,sites
---
```
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

echo "Linking ENV"

if [[ ! -f "{{ releasePath }}"/.env && -f "{{ projectPath }}/.env" ]]; then
  chown {{ username }}:{{ username }} {{ projectPath }}/.env
  chmod 0600 {{ projectPath }}/.env
  ln -s "{{ projectPath }}/.env" "{{ releasePath }}/.env"
  echo "Successfully linked {{ releasePath }}/.env to {{ projectPath }}/.env"
else
  echo "Env path {{ releasePath }}/.env is already linked to {{ projectPath }}/.env"
fi

echo "Install NPM Packages"

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

echo "Prepare Build"

cd "{{ appFolderPath }}"
# link .env file if not already linked
echo 'Checking {{ projectPath }}/.env file'
if [ -f "{{ projectPath }}"/.env ]; then
  echo 'Found .env file in {{ projectPath }}'
else
  echo "Didn't find the .env file in {{ projectPath }}"
  # See if .env file is pushed, if so warn the user, use it as a starting template, and then move it to .env.orig.cleavr.
  if [ -f "{{ appFolderPath }}"/.env ]; then
    echo '[WARNING!] It looks like you have pushed .env file. This is *not* considered to be a good practice. We are using this as a starting template and renaming the original to .env.orig.cleavr. {{ projectPath }}/.env is the one we will be using.'
    cp "{{ appFolderPath }}"/.env "{{ projectPath }}"/.env
    mv "{{ appFolderPath }}"/.env "{{ appFolderPath }}"/.env.orig.cleavr
  else
    echo '{{ projectPath }}/.env file does not exist. Creating an empty one for linking.'
cat > {{ projectPath }}/.env << EOF
# This is a default env file = Be sure to update this file for your Adonis app
PORT={{ portNumber }}
HOST=127.0.0.1
NODE_ENV=production
APP_KEY=$(env LC_CTYPE=C tr -dc "A-Za-z0-9_$%^&*()-+=" < /dev/urandom | head -c 32 | base64)
SESSION_DRIVER=cookie
CACHE_VIEWS=true
DRIVE_DISK=local
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_NAME=
DB_DATABASE=
DB_USER=
DB_PASSWORD=
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

echo "Build App"

if [ ! -d "{{ currentPath }}/node_modules" ]; then

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

fi

# link .env file if not already linked
echo 'Checking {{ projectPath }}/.env file'
if [ -f "{{ projectPath }}"/.env ]; then
  echo 'Found .env file in {{ projectPath }}'
else
  echo "Didn't find the .env file in {{ projectPath }}"
  # See if .env file is pushed, if so warn the user, use it as a starting template, and then move it to .env.orig.cleavr.
  if [ -f "{{ appFolderPath }}"/.env ]; then
    echo '[WARNING!] It looks like you have pushed .env file. This is *not* considered to be a good practice. We are using this as a starting template and renaming the original to .env.orig.cleavr. {{ projectPath }}/.env is the one we will be using.'
    cp "{{ appFolderPath }}"/.env "{{ projectPath }}"/.env
    mv "{{ appFolderPath }}"/.env "{{ appFolderPath }}"/.env.orig.cleavr
  else
    echo '{{ projectPath }}/.env file does not exist. Creating an empty one for linking.'
cat > {{ projectPath }}/.env << EOF
# This is a default env file = Be sure to update this file for your Adonis app
PORT={{ portNumber }}
HOST=127.0.0.1
NODE_ENV=production
APP_KEY=$(env LC_CTYPE=C tr -dc "A-Za-z0-9_$%^&*()-+=" < /dev/urandom | head -c 32 | base64)
SESSION_DRIVER=cookie
CACHE_VIEWS=true
DRIVE_DISK=local
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_NAME=
DB_DATABASE=
DB_USER=
DB_PASSWORD=
EOF
fi
fi

cd "{{ appFolderPath }}"
echo 'Building packages'
node "{{ appFolderPath }}/ace" build --production
cd "{{ appFolderPath }}/build"

if [ -f yarn.lock ]; then
  echo 'Found yarn.lock. Installing packages yarn with frozen lockfile'
  yarn install --frozen-lockfile --production
elif [ -f package-lock.json ]; then
  echo 'Found package-lock.json. Installing packages using npm ci'
  npm ci --production
else
  echo 'Missing lock file(s). Installing packages using npm install'
  npm install --production
fi


if [[ ! -f "{{ appFolderPath }}"/build/.env && -f "{{ projectPath }}/.env" ]]; then
  chown {{ username }}:{{ username }} {{ projectPath }}/.env
  chmod 0600 {{ projectPath }}/.env
  ln -s "{{ projectPath }}/.env" "{{ appFolderPath }}/build/.env"
  echo "Successfully linked {{ appFolderPath }}/.env to {{ projectPath }}/.env"
else
  echo "Env path {{ appFolderPath }}/build/.env is already linked to {{ projectPath }}/.env"
fi

echo "Migrate Database"
echo 'Preparing to migrate the database'

if [ -f "{{ projectPath }}"/.env ]; then
  cd "{{ appFolderPath }}/build"
  node ace migration:run --force
else
  echo ".env file is needed to run the migration. Database migration is skipped in this deployment."
fi

BUILD_SERVER_JS_PATH="{{ appFolderPath }}/build/server.js"
if [ -f $BUILD_SERVER_JS_PATH ]; then
echo "Found $BUILD_SERVER_JS_PATH. Linking..."
ln -sf $BUILD_SERVER_JS_PATH {{ appFolderPath }}/server.js > /dev/null 2>&1
fi

ln -sfn "{{ appFolderPath }}" "{{ currentPath }}"
echo "Activated {{ appFolderPath }}"

if [ -f /home/cleavr/.pm2/logs/{{ domain }}-out.log ]; then
  echo "Restarting {{ domain }}..."
  sudo /usr/sbin/service {{ domain }} restart
else
    if [ ! -e {{ currentPath }}/.cleavr.config.js ]; then
    echo "PM2 ecosystem file is not linked. Linking..."
    ln -s {{ projectPath }}/.cleavr.config.js {{ currentPath }}/.cleavr.config.js
    fi
      if [ -f {{ projectPath }}/.pm2_has_changed_since ]; then
        echo "PM2 ecosystem config has changed since the last deployment. Restarting the app..."
        echo "Stopping the old running app if any..."
        env PM2_HOME=/opt/pm2 pm2 del {{ domain }} || true
        echo "Starting the app..."
        env PM2_HOME=/opt/pm2 pm2 start {{ currentPath }}/.cleavr.config.js --update-env
      else
        echo "PM2 ecosystem config hasn't changed since the last deployment. Reloading the app..."
        env PM2_HOME=/opt/pm2 pm2 startOrReload {{ currentPath }}/.cleavr.config.js --update-env
      fi
        rm -f {{ projectPath }}/.pm2_has_changed_since > /dev/null 2>&1
        # Restart if 502
        status=`curl -I -s -L {{ domain }} 2>/dev/null | head -n 1 | cut -d$' ' -f2`
        if [[ $status = "502" ]]; then
          echo 'Force restarting the app'
          env PM2_HOME=/opt/pm2 pm2 delete {{ domain }}
          env PM2_HOME=/opt/pm2 pm2 start {{ currentPath }}/.cleavr.config.js
        fi
fi

cd "{{ releasesPath }}"
rm -rf {{ releasesToPrune }} > /dev/null 2>&1
```

