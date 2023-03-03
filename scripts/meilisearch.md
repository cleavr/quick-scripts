---
title: Meilisearch
description: Install Meilisearch on your server  
runAs: root
tags: search
---

```
# Add Meilisearch package
echo "deb [trusted=yes] https://apt.fury.io/meilisearch/ /" | sudo tee /etc/apt/sources.list.d/fury.list

# Update APT and install Meilisearch
sudo apt update && sudo apt install meilisearch

# Launch Meilisearch
meilisearch
```
