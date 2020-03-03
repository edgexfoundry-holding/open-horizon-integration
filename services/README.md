# _Open Horizon_  services

This  directory contains [Open Horizon](http://github.com/open-horizon) services.

Please see the ["hello world"](docs/HELLO_WORLD.md) example for an introduction to developing for [Open Horizon](http://github.com/open-horizon)

# Status

![](https://img.shields.io/github/license/edgexfoundry-holding/open-horizon-integration.svg?style=flat)
![](https://img.shields.io/github/release/edgexfoundry-holding/open-horizon-integrationsvg?style=flat)
[![Build Status](https://travis-ci.org/edgexfoundry-holding/open-horizon-integration.svg?branch=master)](https://travis-ci.org/edgexfoundry-holding/open-horizon-integration)
[![Coverage Status](https://coveralls.io/repos/github/edgexfoundry-holding/open-horizon-integration/badge.svg?branch=master)](https://coveralls.io/github/edgexfoundry-holding/open-horizon-integration?branch=master)

![](https://img.shields.io/github/repo-size/edgexfoundry-holding/open-horizon-integration.svg?style=flat)
![](https://img.shields.io/github/last-commit/edgexfoundry-holding/open-horizon-integrationsvg?style=flat)
![](https://img.shields.io/github/commit-activity/w/edgexfoundry-holding/open-horizon-integration.svg?style=flat)
![](https://img.shields.io/github/contributors/edgexfoundry-holding/open-horizon-integration.svg?style=flat)
![](https://img.shields.io/github/issues/edgexfoundry-holding/open-horizon-integration.svg?style=flat)
![](https://img.shields.io/github/tag/edgexfoundry-holding/open-horizon-integration.svg?style=flat)

[arm64-shield]: https://img.shields.io/badge/arm64-yes-green.svg
[amd64-shield]: https://img.shields.io/badge/amd64-yes-green.svg
[arm-shield]: https://img.shields.io/badge/arm-yes-green.svg

## 1. Introduction

These services and patterns are built and pushed to designated Docker registry & namespace as well as Open Horizon exchange and organization.  The default build configuration is:

+ `HZN_EXCHANGE_URL` defaults to `https://alpha.edge-fabric.com/v1`
+ `HZN_ORG_ID` is **unspecified** (e.g. `github@dcmartin.com`)
+ `DOCKER_NAMESPACE` is **unspecified** (e.g. [`dcmartin`][docker-dcmartin])
+ `DOCKER_REGISTRY` is **unset** and defaults to `docker.io`

[docker-dcmartin]: https://hub.docker.com/?namespace=dcmartin

This repository works best on a &#63743; macOS computer.  However, macOS need some additional software install the [HomeBrew](http://brew.sh) package manager and install the necessary software:

```
% brew install gettext
% cd /usr/local/bin && ln -s ../Cellar/gettext/0.19.8.1/bin/envsubst .
```

### 1.1 Variables
The `HZN_ORG_ID` and `DOCKER_NAMESPACE` should be specified appropriately prior to any build; substitute values appropriately, for example:

```
% export HZN_ORG_ID="github@dcmartin.com"
% export DOCKER_NAMESPACE="dcmartin"
```

To make those environment variables persistent, copy them into files with the same names:

```
% echo "${HZN_ORG_ID}" > HZN_ORG_ID
% echo "${DOCKER_NAMESPACE}" > DOCKER_NAMESPACE
```

### 1.2 Dependencies
Docker provides for build dependencies through the `FROM ` directive in the `Dockerfile`; most services depend on the base service containers for `base-ubuntu` or `base-alpine`.

Build all services for all architectures from the top-level using the following command:

```
% make service-build
```

# 2. Services & Patterns

Services are defined within a directory hierarchy of this [repository][repository]. All services in this repository share a common [design](docs/DESIGN.md).

+ [`hello`](./hello/README.md) - The "hello world" example
+ [`esstest`](./esstest/README.md) - A simple test of the [edge-sync-service](http://github.com/open-horizon/edge-sync-service)

There are _base_ containers that are used by the other services:

+ [`base-alpine`](./base-alpine/README.md) - base container for Alpine LINUX
+ [`base-ubuntu`](./base-ubuntu/README.md) - base container for Ubuntu LINUX
+ [`apache-ubuntu`](./apache-ubuntu/README.md) - Apache Web server for Ubuntu
+ [`apache-alpine`](./apache-alpine/README.md) - Apache Web server for Alpine

#  Further Information 

See [`SERVICE.md`][service-md] and [`PATTERN.md`][pattern-md] for more information on building services and patterns.
Refer to the following for more information on [getting started][edge-fabric] and [installation][edge-install].

# Changelog & Releases

Releases are based on Semantic Versioning, and use the format
of ``MAJOR.MINOR.PATCH``. In a nutshell, the version will be incremented
based on the following:

- ``MAJOR``: Incompatible or major changes.
- ``MINOR``: Backwards-compatible new features and enhancements.
- ``PATCH``: Backwards-compatible bugfixes and package updates.

## Authors & contributors

David C Martin (github@dcmartin.com)

[dcmartin]: https://github.com/dcmartin
[issue]: https://github.com/edgexfoundry-holding/open-horizon-integration/issues
[open-horizon]: http://github.com/open-horizon/
[service-md]: docs/SERVICE.md
[cicd-md]: docs/CICD.md
[pattern-md]: docs/PATTERN.md
