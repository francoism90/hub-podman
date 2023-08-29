# Hub

## Introduction

Hub is a video on demand (VOD) media distribution system that allows users to access to videos, television shows and films.

Please browse the following repositiories to learn more:

- [Hub](https://github.com/francoism90/hub) - Podman/Docker instance
- [Api](https://github.com/francoism90/hub-api) - Laravel App

> NOTE: Hub is unstable and in active development, expect bumps on the road.

## Prerequisites

> NOTE: See [Docker Compose](https://github.com/francoism90/hub/wiki/Docker-Compose) if only Docker is available.

- [Podman](https://podman.io/) with SELinux support
- [Podman Compose](https://github.com/containers/podman-compose)
- Running in rootless mode:
  - <https://github.com/containers/podman/blob/main/docs/tutorials/rootless_tutorial.md>
  - <https://wiki.archlinux.org/title/Podman#Rootless_Podman>
- [mkcert](https://github.com/FiloSottile/mkcert)
- DNS-server (recommended) or edit your `hosts` file

## Installation

### Clone repository

Clone the repository, for example to the Code directory of the home-folder:

```bash
cd ~/Code
git clone --recurse-submodules https://github.com/francoism90/hub.git
```

Update the Podman environment settings to your own needs:

```bash
cd ~/Code/hub
cp .env.example .env
vi .env
```

### DNS records

The following DNS/host-file records should match the machine running the instance:

```md
192.168.1.100 hub.test
192.168.1.100 ws.hub.test
```

### Local certificate

Create a script to manage your local certificates, e.g. `cert.sh`, and replace `192.168.1.100` with the device's IP-address:

```bash
#!/bin/sh
mkcert -install \
&& mkcert -key-file key.pem -cert-file cert.pem \
  hub.test *.hub.test \
  192.168.1.100 \
  127.0.0.1 ::1
```

Execute the script:

```bash
chmod +x cert.sh
./cert.sh
```

Generate an one-time `dhparam.pem` file:

```bash
openssl dhparam -out dhparam.pem 2048
```

Copy the generated files, into the the `~/Code/hub/ssl` folder.

> **TIP:** You may want to setup [mobile devices](https://github.com/FiloSottile/mkcert#mobile-devices).

### Configuring App

Update the Laravel environment settings to your own needs:

```bash
cd ~/Code/hub/src/api
cp .env.example .env
vi .env
```

To start Hub:

```bash
hub up -d
hub a migrate --seed
hub a npm run build
```

The Hub instance should be available at <https://hub.test>.

## Interaction

> TIP: You may want to add the following alias `alias hub='[ -f hub ] && sh hub || sh bin/hub'`

Hub comes with it's own Laravel Sail utility clone: `hub` (`bin/hub`).

It is designed to work exclusively with Podman.

### Podman

To build Hub:

```bash
cd ~/Code/hub
hub build --no-cache
```

To start Hub:

```bash
hub up -d
```

To stop Hub:

```bash
hub down
```

One may use the same Laravel Sail syntax, to enter and perform Laravel operations:

```bash
hub artisan make:controller TestController
hub a make:controller TestController
hub a migrate
hub npm run dev
hub npm run build
hub shell
hub help
```
