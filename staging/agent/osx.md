# Install the Open Horizon Agent (Anax) on OSX

This page describes the installation of the Open Horizon Agent (Anax) on OSX. These instructions have been tested with **Catalina** version.

> This installation should be done on a separate machine than that of Open Horizon hub to avoid environment variable and port collisions.

## Pre-requisites

1. Install the `brew` package manager if not installed already.

``` bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

2. Install `wget` and `socat` utilities.

```bash
brew update
brew upgrade
brew install wget
brew install socat
```

3. [Get and install](https://docs.docker.com/docker-for-mac/install/) Docker

``` bash
wget https://download.docker.com/mac/stable/Docker.dmg
```

4. Test after install

``` bash
docker --version
```

The response should be similar to the following:

```bash
Docker version 19.03.5, build 633a0ea
```

Check if Docker daemon is running with `docker info`. Look for value of `Running` in `Server` section. The output should look like this.

```bash
Client:
 Debug Mode: false

Server:
 Containers: 22
  Running: 1
  Paused: 0
  Stopped: 21
 Images: 71
 Server Version: 19.03.5
...
```

## Installation

1. Download and install the certificate for Agent

``` bash
wget http://pkg.bluehorizon.network/macos/certs/horizon-cli.crt
sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain horizon-cli.crt
```

2. Download and install the Agent

_Can we look-up version number for `horizon-cli`?_

``` bash
wget http://pkg.bluehorizon.network/macos/horizon-cli-2.24.18.pkg
sudo installer -pkg "horizon-cli-2.24.18.pkg" -target /
```

The output of running the command should look something like this:

```bash
installer: Package name is horizon-cli-2.24.18
installer: Installing at base path /
installer: The install was successful.
```

## Configure Open Horizon Agent (Anax)

1. Create a file at `/etc/default/horizon`

``` bash
sudo mkdir -p /etc/default
sudo touch /etc/default/horizon
```

2. Add the following contents to `/etc/default/horizon`. Replace `x.x.x.x` with the actual IP address of the machine running the Open Horizon Hub Services.

```bash
HZN_EXCHANGE_URL=http://x.x.x.x:3090/v1/
HZN_FSS_CSSURL=http://x.x.x.x:9443
```

## Run the Open Horizon Agent (Anax)

1. Start the Open Horizon Agent (Anax) using the command below. It will start the Open Horizon Agent (Anax) with default file as `/etc/default/horizon` and index number as 1.

_Start with a different file without index_

``` bash
horizon-container start
```

The output should look like this.

```bash
Starting socat to listen on port 2375 and forward it to the docker API socket...
latest: Pulling from openhorizon/amd64_anax
3b37166ec614: Pull complete 
504facff238f: Pull complete 
.... 
b487343afe3e: Pull complete 
Digest: sha256:ed73ca95f2550acf8b1947c822e17011ca4ac45ac84a7f1d983ee82dc7693bc3
Status: Downloaded newer image for openhorizon/amd64_anax:latest
docker.io/openhorizon/amd64_anax:latest
Starting the Horizon agent container openhorizon/amd64_anax:latest...
6374638aed9f702722ca3f6a7ccf4528dbbc6236ead153ffe7724526cbbfe052
Horizon agent started successfully. Now use 'hzn node list', 'hzn register ...', and 'hzn agreement list'
```

4. If you had a previous version already installed, then update the Open Horizon Agent (Anax) and restart with the commands below.

``` bash
horizon-container update
horizon-container restart
```

5. Check version after installation

Check version with the command below.

``` bash
hzn version
```

Output:

```bash
Horizon CLI version: 2.24.18
Horizon Agent version: 2.24.18
```

6. Check the list of nodes after installation

```bash
hzn node list
```

Output:

```json
{
  "id": "",
  "organization": null,
  "pattern": null,
  "name": null,
  "token_last_valid_time": "",
  "token_valid": null,
  "ha": null,
  "configstate": {
    "state": "unconfigured",
    "last_update_time": ""
  },
  "configuration": {
    "exchange_api": "http://54.xxx.xxx.xxx:3090/v1/",
    "exchange_version": "",
    "required_minimum_exchange_version": "2.11.1",
    "preferred_exchange_version": "2.11.1",
    "mms_api": "http://54.xxx.xxx.xxx:9443",
    "architecture": "amd64",
    "horizon_version": "2.24.18"
  },
  "connectivity": {
    "firmware.bluehorizon.network": true,
    "images.bluehorizon.network": true
  }
}
```

Look at the value of `exchange_api` in `configuration` in the output of `hzn node list`. (If you have `jq` installed, then you can get this value directly with this command `hzn node list | jq -r .configuration.exchange_api`.) The value should match with what was added in `/etc/default/horizon`.

_Any way to check from the Hub side?_
