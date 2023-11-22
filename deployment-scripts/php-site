---
title: Deploy Php Sites
description: This script will copy the project from repo, links environment, activates deployment and cleans older deployment 
tags: deployment, php, sites
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

if [[ ! -f "{{ releasesPath }}"/.env && -f "{{ projectPath }}/.env" ]]; then
  chown {{ username }}:{{ username }} {{ projectPath }}/.env
  chmod 0600 {{ projectPath }}/.env
  ln -s "{{ projectPath }}/.env" "{{ releasesPath }}/.env"
  echo "Successfully linked {{ releasesPath }}/.env to {{ projectPath }}/.env"
else
  echo "Env path {{ releasesPath }}/.env is already linked to {{ projectPath }}/.env"
fi

echo "Activate New Deployment"

ln -sfn "{{ appFolderPath }}" "{{ currentPath }}"
echo "Activated {{ appFolderPath }}"
cd "{{ releasesPath }}"
echo 'Reloading PHP-FPM'
sudo service php{{ phpVersion }}-fpm reload

echo "Clean Old Deployments"
cd "{{ releasesPath }}"
echo "Cleaning up {{ releasesToPrune }}"
rm -rf {{ releasesToPrune }} > /dev/null 2>&1
```
