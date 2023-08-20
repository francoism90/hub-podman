# Hub

## Introduction

Hub is a Video on demand (VOD) media distribution system that allows users to access to videos, television shows and films.

## Prerequisites

- [Podman](https://podman.io/)
- [Podman Compose](https://github.com/containers/podman-compose)
- [git](https://git-scm.com/downloads)

## Installation

Clone this repository:

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

## Usage

> TIP: You may want to add the following alias `alias hub='[ -f hub ] && sh hub || sh bin/hub'`

Hub comes with it's own Laravel Sail utility clone '`hub`'.

It is designed to work with Podman, instead of Docker.

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
