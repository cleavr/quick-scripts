---
title: Deploy Static Sites
description: This script will copy the project from repo, activates deployment and cleans older deployment
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
echo "dir exists symlink ..."
  # if it's not a link, then we'll just back it up
  if [ ! -L "{{ currentPath }}" ]; then
    echo "Found an existing {{ currentPath }} directory. Removing it."
    rm -rf "{{ currentPath }}" > /dev/null 2>&1
  fi
fi

chmod 0600 /home/{{ username }}/.ssh/id_rsa > /dev/null 2>&1

echo "Activating the deployment"
ln -sfn "{{ appFolderPath }}" "{{ currentPath }}"
echo "Activated {{ appFolderPath }}"

echo "Clean Old Deployments"
cd "{{ releasesPath }}"
echo "Cleaning up {{ releasesToPrune }}"
rm -rf {{ releasesToPrune }} > /dev/null 2>&1
```
