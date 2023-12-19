# Hub

## Introduction

Hub is a video on demand (VOD) media distribution system that allows users to store videos.

> **NOTE:** See <https://github.com/francoism90/.github/tree/main/hub> for (WIP) screenshots.

Please checkout the following repositiories to learn more:

- [Hub](https://github.com/francoism90/hub) - Podman/Docker instance
- [Api](https://github.com/francoism90/hub-api) - Laravel Livewire + API

## Prerequisites

> NOTE: Docker should work, but has been untested.

- Linux (WSL2 has been untested)
- [Podman](https://podman.io/) with SELinux support
- [Podman Compose](https://github.com/containers/podman-compose)
- Running in rootless mode:
  - <https://github.com/containers/podman/blob/main/docs/tutorials/rootless_tutorial.md>
  - <https://wiki.archlinux.org/title/Podman#Rootless_Podman>
- [mkcert](https://github.com/FiloSottile/mkcert)
- DNS-server (recommended) or edit your `hosts` file

## Interaction

Hub comes with it's own Laravel Sail utility clone: `hub` (`bin/hub`).

> **TIP:** You may want to add the following alias `alias hub='[ -f hub ] && sh hub || sh bin/hub'` to your `~/.zshrc`

It is designed to work exclusively with Podman and Podman Compose.

## Installation

### Clone repository

Clone the repository, for example to `/home/<user>/Code/hub`:

```bash
cd ~/Code
git clone --recurse-submodules https://github.com/francoism90/hub.git
```

If the API repository doesn't exists (yet):

```bash
cd ~/Code/hub
git clone https://github.com/francoism90/hub-api.git app
```

Configure the Hub services:

```bash
cp .env.example .env
vi .env
```

If you only want to access Hub on your local machine, add the following `hosts` entry:

```md
127.0.0.1 hub.test ws.hub.test
```

The following DNS-records should be added of the machine running the instance if you want to expose it on your LAN, e.g.:

```md
192.168.1.100 hub.test ws.hub.test
```

### Create certificate

To protect and use the hub instance, it is required to create a certificate.

> **NOTE:** This is not required when using your own domain and issuer.

Create a script to manage your local certificate, e.g. `cert.sh`:

```bash
#!/bin/sh
mkcert -install \
&& mkcert -key-file key.pem -cert-file cert.pem \
  hub.test *.hub.test \
  localhost \
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

Copy the generated files or place your own certificate, into the `~/Code/hub/ssl` folder.

> **TIP:** You may want to setup [mobile devices](https://github.com/FiloSottile/mkcert#mobile-devices).

### Configuring App

Update the Laravel environment settings to your own needs:

```bash
cd ~/Code/hub/app
cp .env.example .env
vi .env
```

Build Hub using:

```bash
hub build --no-cache
```

To start Hub:

```bash
hub up -d
hub composer i
hub a key:generate
hub a storage:link
hub a migrate --seed
hub a npm run build
hub a scout:sync
```

You may need to alter permissions when using SELinux:

```bash
cd ~/Code/hub
chcon -Rt container_file_t data/
```

The Hub instance should be available at <https://hub.test>.

The following administrator links are available:

- <https://hub.test/admin> - Filament Panel
- <https://hub.test/horizon> - Laravel Horizon (super-admin only)
- <https://hub.test/telescope> - Laravel Telescope (super-admin only)

### Cheat sheet

To build Hub:

```bash
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

You may want to use a different mount-point for media storage, without adjusting `docker-compose.yml`:

```bash
sudo mount --bind /mnt/data/videos/media ~/Code/hub/data/media -o x-gvfs-hide
podman system migrate
hub up -d
```
