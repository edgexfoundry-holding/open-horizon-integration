# Install the Open Horizon Hub Services

## Pre-requisites

### Open Horizon Hub Services (Exchange, Switchboard, AgBots, Sync Service)
+ OS: Ubuntu server, latest build recommended.  Instructions assume this.
+ VM: 4Gb RAM, 20Gb storage, 1vCPU, root access
+ NOTE: Ensure you are not installing and running this on a system that already has a service using port 80.

### Open Horizon Agent (Anax)
+ OS: Ubuntu server or desktop (latest build recommended), or OSX.  
+ VM: 1Gb RAM, 10Gb storage, 1vCPU, root access

## Initial setup

Stand up your environment for the Horizon Hub Services and open a shell to update utilities.
*NOTE*: You will need to perform all of these tasks as root.

If you clone this repository, it will be easier to modify and use the configuration and resource files. 
The following instructions will assume that you have done so and that the contents are in `./horizon-edgex`

``` bash
git clone https://github.com/edgexfoundry-holding/open-horizon-integration.git
```

### Install Prerequisites

NOTE: If you are not already running as root, please become root with `sudo -s` or similar.

``` bash
apt-get -y update
apt-get -y install apache2-utils jq curl make gcc
```

### Install Container Engine

``` bash
curl -fsSL get.docker.com | sh
apt-get install -y docker-compose
```

## Next

[Build and Run](02-build-and-run-horizon.md) the Open Horizon Hub Services.
