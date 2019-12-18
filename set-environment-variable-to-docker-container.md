# How to set environment variable to a Docker container

Use `-e` parameter:

```
docker run -e CUSTOM_ENV_VAR="Hello, world" busybox /bin/sh -c "echo \$CUSTOM_ENV_VAR"
```
