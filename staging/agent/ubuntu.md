# Install the Open Horizon Agent (Anax) on Ubuntu

This page describes the installation of the Open Horizon Agent (Anax) on Ubuntu. These instructions are tested on 64-bit releases of Ubuntu Bionic (18.04) and Xenial (16.04).

> This installation should be done on a separate machine than that of Open Horizon hub to avoid environment variable and port collisions.

## Pre-requisites

The following pre-requisites apply for the machine where the agent is to be installed.

1. Version: Latest build recommended
2. RAM: 1 GB minimum
3. Storage: 8GB minimum
4. Root access - use `sudo -s`

## Installation

1. Update the package manager

``` bash
apt-get -y update
```

2. Install and test Docker

``` bash
curl -fsSL get.docker.com | sh
docker --version
```

3. Configure package manager for the Open Horizon (Anax) Agent using the commands below.

For Ubuntu 18.x (Bionic) release, use the commands as shown below.

> If you did not copy the CRLF after `EOF`, then you will need to press Enter/Return on your keyboard.

``` bash
wget -qO - http://pkg.bluehorizon.network/bluehorizon.network-public.key | apt-key add -
aptrepo=testing
cat <<EOF > /etc/apt/sources.list.d/bluehorizon.list
deb [arch=$(dpkg --print-architecture)] http://pkg.bluehorizon.network/linux/ubuntu bionic-$aptrepo main
deb-src [arch=$(dpkg --print-architecture)] http://pkg.bluehorizon.network/linux/ubuntu bionic-$aptrepo main
EOF

```

For Ubuntu 16.x (Xenial) release, change the commands as shown below.

> If you did not copy the CRLF after `EOF`, then you will need to press Enter/Return on your keyboard.

``` bash
wget -qO - http://pkg.bluehorizon.network/bluehorizon.network-public.key | apt-key add -
aptrepo=testing
cat <<EOF > /etc/apt/sources.list.d/bluehorizon.list
deb [arch=$(dpkg --print-architecture)] http://pkg.bluehorizon.network/linux/ubuntu xenial-$aptrepo main
deb-src [arch=$(dpkg --print-architecture)] http://pkg.bluehorizon.network/linux/ubuntu xenial-$aptrepo main
EOF

```

4. Refresh package manager index list

``` bash
apt-get -y update
```

## Configure Open Horizon Agent (Anax)

1. Create a file at `/etc/default/horizon`

``` bash
mkdir -p /etc/default
touch /etc/default/horizon
```

2. Add the following contents to `/etc/default/horizon`. Replace `x.x.x.x` with the actual IP address of the machine running the Open Horizon Hub Services.

```bash
HZN_EXCHANGE_URL=http://x.x.x.x:3090/v1/
HZN_FSS_CSSURL=http://x.x.x.x:9443
HZN_DEVICE_NODE=mynode
```

See also [#25](https://github.com/edgexfoundry-holding/open-horizon-integration/issues/25).

## Install and run the agent

5. Install and run the Agent with the following command

``` bash
apt-get -y install bluehorizon
```

You maybe prompted with the following message. Just hit **Enter** to continue installation with the configuration file that was updated above i.e. `/etc/default/horizon`.

```bash
Configuration file '/etc/default/horizon'
 ==> File on system created by you or by a script.
 ==> File also in package provided by package maintainer.
   What would you like to do about it ?  Your options are:
    Y or I  : install the package maintainer's version
    N or O  : keep your currently-installed version
      D     : show the differences between the versions
      Z     : start a shell to examine the situation
 The default action is to keep your current version.
*** horizon (Y/I/N/O/D/Z) [default=N] ?  
```

See also [#29](https://github.com/edgexfoundry-holding/open-horizon-integration/issues/29).

6. Exit out of root, back to your user account

``` bash
exit
```

7. Check the version to confirm that the Horizon CLI is installed and working

``` bash
hzn version
```

You should see output as below.

```bash
Horizon CLI version: 2.25.0
Horizon Agent version: 2.25.0
```

Check to ensure that it is working and your machine/node is "unconfigured".

``` bash
hzn node list
```

You should see output as below.

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
    "exchange_api": "http://3.xxx.xxx.xxx:3090/v1/",
    "exchange_version": "",
    "required_minimum_exchange_version": "2.13.0",
    "preferred_exchange_version": "2.13.0",
    "mms_api": "http://3.xxx.xxx.xxx:9443",
    "architecture": "amd64",
    "horizon_version": "2.25.0"
  },
  "connectivity": {
    "firmware.bluehorizon.network": true,
    "images.bluehorizon.network": true
  }
}
```
