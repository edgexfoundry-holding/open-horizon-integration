# Install the Open Horizon Agent

This continues the instructions from [Install the Open Horizon Hub Services](01-horizon-services-setup.md) and 
[Build and Run](02-build-and-run-horizon.md) the Open Horizon Hub Services.

Stand up your environment in the other tier for the Horizon Agent (Anax) and open a shell.  
Do not attempt this in the same environment as the Horizon Services 
due to port conflicts and environment variable collisions.
Instructions below are for either Ubuntu or OSX.

## For OSX

Install brew package manager

``` bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

brew update
brew upgrade
brew install wget
brew install socat
```

[Get and install](https://docs.docker.com/docker-for-mac/install/) Docker

``` bash
wget https://download.docker.com/mac/stable/Docker.dmg
```

Test after install

``` bash
docker --version
```

Download and install the Agent's certificate

``` bash
wget http://pkg.bluehorizon.network/macos/certs/horizon-cli.crt
sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain horizon-cli.crt
```

Download and install the Agent

``` bash
wget http://pkg.bluehorizon.network/macos/horizon-cli-2.24.18.pkg
sudo installer -pkg "horizon-cli-2.24.18.pkg" -target /
```

The output of running the command should look something like this:

```
installer: Package name is horizon-cli-2.24.18
installer: Upgrading at base path /
installer: The upgrade was successful.
```

If you are installing the Agent for the first time,
start the client using the command below:

``` bash
horizon-container start
```

If you had a previous version already installed, and are upgrading the Agent, 
use the following instead:

``` bash
horizon-container update
horizon-container restart
```

Test after install

``` bash
hzn version

hzn node list
```

When you look at the output for `hzn node list`, pay attention to the line for the exchange api:

``` json
"exchange_api": "https://alpha.edge-fabric.com/v1/"
```

When the Anax agent is properly configured, 
it will point to the public IP address of the Horizon Hub Services that you stood up earlier instead of `alpha.edge-fabric.com`, 
and is useful for confirming proper configuration.

To fix, stop the container, create a file at `/etc/default/horizon` with the contents below, then restart the container and test.

``` bash
horizon-container stop
sudo mkdir -p /etc/default
sudo touch /etc/default/horizon
```

Then edit the `/etc/default/horizon` file and insert the following contents. 

NOTE: Replace `x.x.x.x` with the actual IP address of the machine running the Open Horizon Hub Services.

```
HZN_EXCHANGE_URL=http://x.x.x.x:3090/v1/
HZN_FSS_CSSURL=http://x.x.x.x:9443
```

Then re-start the container:

``` bash
horizon-container start
```

And then check the output to confirm that the exchange api value is now set to your exchange service public IP address:

``` bash
hzn node list
```

-----

## For Ubuntu

IMPORTANT, DO NOT SKIP THIS STEP

Become root so the following several steps will work properly

``` bash
sudo -s
```

Update the package manager

``` bash
apt-get -y update
```

Install dependencies

``` bash
apt-get -y install jq
```

Install and test Docker

``` bash
curl -fsSL get.docker.com | sh
docker --version
```

Install the Anax Agent:

*NOTE* If your operating system is Ubuntu xenial (16.x) then use `dist=xenial` below.

``` bash
arch=$(dpkg --print-architecture)
dist=bionic
version=2.24.18
wget http://pkg.bluehorizon.network/linux/ubuntu/pool/main/h/horizon/bluehorizon_${version}~ppa~ubuntu.${dist}_all.deb
wget http://pkg.bluehorizon.network/linux/ubuntu/pool/main/h/horizon/horizon-cli_${version}~ppa~ubuntu.${dist}_${arch}.deb
wget http://pkg.bluehorizon.network/linux/ubuntu/pool/main/h/horizon/horizon_${version}~ppa~ubuntu.${dist}_${arch}.deb
dpkg -i horizon-cli_${version}~ppa~ubuntu.${dist}_${arch}.deb
dpkg -i horizon_${version}~ppa~ubuntu.${dist}_${arch}.deb
dpkg -i bluehorizon_${version}~ppa~ubuntu.${dist}_all.deb
```

IMPORTANT, exit out of root, back to your user account

``` bash
exit
```

Check the version to confirm that the Horizon CLI is installed and working

``` bash
hzn version
```

Check to ensure that it is working and your machine/node is "unconfigured"

``` bash
hzn node list
```
When you look at the output for `hzn node list`, pay attention to the line for the exchange api:

``` json
"exchange_api": ""
```

When the Agent is properly configured, 
it will point to the public IP address of the Horizon Hub Services that you stood up earlier, and is useful for confirming proper configuration.

To fix this, you will edit the horizon agent configuration file and then restart the agent service.

Please note two important details: first, the protocol is `http` instead of `https` (due to lack of a public signed cert), 
and second, the URL *must* end with a trailing slash, even though the corresponding environment variable does not.  

NOTE: Replace `x.x.x.x` with the actual IP address of the machine running the Open Horizon Hub Services.

Edit the file at `/etc/default/horizon` using `sudo` to set the exchange URL (HZN_EXCHANGE_URL) to `http://x.x.x.x:3090/v1/`.

Also add the MSS URL and set your device id.

Edit the file at `/etc/default/horizon` using `sudo` to set the MMS URL (HZN_FSS_CSSURL) to `http://x.x.x.x:9443`.
Edit the file at `/etc/default/horizon` using `sudo` to set the device id (HZN_DEVICE_ID) to whatever you want, e.g. mynode.

Restart the Agent service:

``` bash
sudo systemctl restart horizon
```

and confirm that the changes took effect by re-running `hzn node list` and checking the `exchange_api` value.

Also, verify that the "exchange_version" is correct.
If it is an empty string, then the agent does not have network connectivity to the exchange.
You will need to resolve this problem before you continue.

# Next

[Configure the Agent](04-configure-anax.md).
