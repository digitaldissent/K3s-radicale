# Radicale for K3s [Broken-Do Not Use]

This is a small docker image for [Radicale](https://github.com/Kozea/Radicale) and easy deployment. Bcrypt authentication inclusive.

## Installation

```sh
# .env
USER=foo
PASSWORD=secret
```

```yaml
# docker-compose.yml
version: '3.8'

services:
  app:
    image: cupcakearmy/radicale:1
    restart: unless-stopped
    env_file: .env
    volumes:
      - ./data:/data
    ports:
      - 80:5232
```

The env file is not strictly required, but will generate a user with its bcrypt password if missing.

## Traefik

An example for traefik (v1) can found below.

```yaml
version: '3.8'

networks:
  proxy:
    external: true

services:
  app:
    image: cupcakearmy/radicale:1
    restart: unless-stopped
    env_file: .env
    volumes:
      - ./data:/data
    networks:
      - proxy
    labels:
      - traefik.enable=true
      - traefik.http.routers.radicale.rule=Host(`radicale.example.org`)
      - traefik.http.routers.radicale.entrypoints=secure
      - traefik.http.routers.radicale.tls.certresolver=le
```

## Customize

### Config

You can overwrite the config by simply mounting it into `/etc/radicale/config`.

### User Auth

The default script only creates a default user with the given `USER` and `PASSWORD` credentials. For multiple users you need to create the files manually. In the default config this is set to `/data/users`.

You can add users with `htpasswd -bB -c /data/users user pass`. And then mount that file to `/data/users` or whatever you have set in the Radicale config file `filesystem_folder`, [more here](https://radicale.org/3.0.html#documentation/configuration/auth/htpasswd_filename).
