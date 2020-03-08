# Install the Open Horizon Agent (Anax) on Ubuntu

This page describes the installation of the Open Horizon Agent (Anax) on Ubuntu. These instructions have been tested with ...

> This installation should be done on a separate machine than that of Open Horizon hub to avoid environment variable and port collisions.

## Pre-requisites

The following pre-requisites apply for the machine where the agent is to be installed.

1. Version: Latest build recommended
2. RAM: 4 GB
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

> If you did not copy the CRLF after `EOF`, then you will need to press Enter/Return on your keyboard.

``` bash
wget -qO - http://pkg.bluehorizon.network/bluehorizon.network-public.key | apt-key add -
aptrepo=testing
cat <<EOF > /etc/apt/sources.list.d/bluehorizon.list
deb [arch=$(dpkg --print-architecture)] http://pkg.bluehorizon.network/linux/ubuntu xenial-$aptrepo main
deb-src [arch=$(dpkg --print-architecture)] http://pkg.bluehorizon.network/linux/ubuntu xenial-$aptrepo main
EOF

```

For Ubuntu 18.x (Bionic) release, change the commands as shown below.

> If you did not copy the CRLF after `EOF`, then you will need to press Enter/Return on your keyboard.

``` bash
wget -qO - http://pkg.bluehorizon.network/bluehorizon.network-public.key | apt-key add -
aptrepo=testing
cat <<EOF > /etc/apt/sources.list.d/bluehorizon.list
deb [arch=$(dpkg --print-architecture)] http://pkg.bluehorizon.network/linux/ubuntu bionic-$aptrepo main
deb-src [arch=$(dpkg --print-architecture)] http://pkg.bluehorizon.network/linux/ubuntu bionic-$aptrepo main
EOF

```

4. Refresh package manager index list

``` bash
apt-get -y update
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
HZN_DEVICE_NODE=mynode
```

_`HZN_DEVICE_NODE` is not set in installation of agent on Mac_

## Install and run the agent

5. Install and run the Agent with the following command

_Installation of agent on Mac requires certificate; not here, why?_

``` bash
apt-get -y install bluehorizon
```

_Does this installation also trigger `horizon-container start`?_

6. Exit out of root, back to your user account

``` bash
exit
```

7. Check the version to confirm that the Horizon CLI is installed and working

``` bash
hzn version
```

Check to ensure that it is working and your machine/node is "unconfigured"

``` bash
hzn node list
```

