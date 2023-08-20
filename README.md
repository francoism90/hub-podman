# Hub

## Introduction

Hub is a video on demand (VOD) media distribution system that allows users to access to videos, television shows and films.

Please browse the following repositiories to learn more, as this is only the Podman instance:

- [Api](https://github.com/francoism90/hub-api) - Laravel API backend
- [App](https://github.com/francoism90/hub-app) - VueJS frontend

## Prerequisites

- [Podman](https://podman.io/) with SELinux support
- [Podman Compose](https://github.com/containers/podman-compose)
- Running in rootless mode:
  - <https://github.com/containers/podman/blob/main/docs/tutorials/rootless_tutorial.md>
  - <https://man.archlinux.org/man/podman.1#Rootless_mode>
- [mkcert](https://github.com/FiloSottile/mkcert)
- DNS-server (recommended) or edit your `hosts` file

## Installation

### Clone repository

Clone the repository:

```bash
cd ~/Code
git clone --recurse-submodules https://github.com/francoism90/hub.git
```

Update the environment settings:

```bash
cd ~/Code/hub
cp .env.example .env
vi .env
```

### DNS records

The following DNS-records should match the machine running the instance:

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

Copy the generated files, into the the `ssl` folder.

> **TIP:** You may want to setup [mobile devices](https://github.com/FiloSottile/mkcert#mobile-devices).

## Usage

> TIP: You may want to add the following alias `alias hub='[ -f hub ] && sh hub || sh bin/hub'`

Hub comes with it's own Laravel Sail utility clone: `hub`.

It is designed to work exclusively with Podman, instead of Docker.

### Podman

To build Hub:

```bash
cd ~/Code/hub
bin/hub build --no-cache
```

To start Hub:

```bash
bin/hub up -d
```

To stop Hub:

```bash
bin/hub down
```

### Laravel

One may use the same Laravel Sail syntax, to enter and perform Laravel operations:

```bash
bin/hub artisan make:controller TestController
bin/hub a make:controller TestController
bin/hub a migrate
bin/hub shell
bin/hub help
```
