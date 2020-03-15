# Register agent with Hub

This page follows the [publication](./hub.md) of an EdgeX service to the hub. This page covers the steps required to register an Open Horizon agent with hub so that it starts executing the service. The service to be executed will be the one deployed to hub.

## Pre-requisites

A running agent e.g. [on Ubuntu](../agent/ubuntu.md) is configured to communicate with the hub.

## Getting started

Clone this repository with `git clone https://github.com/edgexfoundry-holding/open-horizon-integration` and follow the steps to register agent with hub.

1. Manually create the volumes as required by the service.

```bash
docker volume create db-data
docker volume create log-data
docker volume create consul-data
docker volume create consul-config
```

2. Verify the volumes have been created with `docker volume list`.

3. Manually setup and open the permissions on the host directories being bound with the command below.

```bash
mkdir -p /var/run/edgex/logs
mkdir -p /var/run/edgex/data
mkdir -p /var/run/edgex/consul/data
mkdir -p /var/run/edgex/consul/config
mkdir -p /root/res
chmod -R a+rwx /var/run/edgex
chmod -R a+rwx /root/res
```

4. Copy the configuration files from the `res` folder in this repository to the `/root/res folder` on the host.

5. Register the agent.

```bash
hzn register -p pattern-edgex-amd64 --policy ./configs/node.policy
```

6. To confirm that your edge node is registered for the pattern, run `hzn node list` and confirm that the response shows your node ID. and that you're configured for the testorg/pattern-edgex-amd64 pattern.

7. To check on the status of the agreement, run `hzn agreement list`. Optionally, to see verbose output run `hzn eventlog list`.

8. Once the agreement is finalized, to see the running containers, run `sudo docker ps`.
