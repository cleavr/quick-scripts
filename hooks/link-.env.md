---
title: Link .env 
description: This hook will create a link from your project's .env file, if exists, to current deployment directory. You should add this hook before Activate New Deployment hook.
tags: deployment, adonis, laravel, nodejs
---

```
cd {{ releasePath }}

ENV_SRC_PATH="{{ projectPath }}/.env"
ENV_PATH="{{ releasePath }}/.env"

if [[  -f "$ENV_SRC_PATH" && ! -f "$ENV_PATH" ]]; then
  ln -s "$ENV_SRC_PATH" "$ENV_PATH"
  echo "Successfully linked $ENV_PATH to $ENV_SRC_PATH"
fi
```
