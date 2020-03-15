# Publish EdgeX service to the Open Horizon Hub

This page follows the successful installation of a hub (e.g. on [Ubuntu on AWS EC2](../hub/aws-ec2-linux-vm.md)) and an agent (e.g. on [Ubuntu](../agent/ubuntu.md)). This page covers the steps for publishing an example EdgeX service to the Open Horizon hub and then tell the agent to run the service.

- [Publish EdgeX service to the Open Horizon Hub](#publish-edgex-service-to-the-open-horizon-hub)
  - [Pre-requisites](#pre-requisites)
  - [Publish service](#publish-service)

## Pre-requisites

Confirm that the hub has the following environment variables set-up and persisted. The following list is a subset of the variables that are actually set-up when installing the hub.

- `HZN_EXCHANGE_URL` set to the actual IP address of the machine running Open Horizon Hub services.
- `HZN_ORG_ID` set to an organization e.g. `testorg`
- `HZN_EXCHANGE_USER_AUTH` set to user credentials e.g. `admin:adminpw`

Run `export ORG_ID=testorg`. See also [#34](https://github.com/edgexfoundry-holding/open-horizon-integration/issues/34).

Run `hzn exchange user list` to verify. The output should look like this.

```json
{
  "testorg/admin": {
    "admin": true,
    "email": "admin@testorg",
    "lastUpdated": "2019-08-19T12:25:06.754Z[UTC]",
    "password": "********",
    "updatedBy": "root/root"
  }
}
```

## Publish service

Clone this repository with `git clone https://github.com/edgexfoundry-holding/open-horizon-integration` and follow the steps to publish the service.

1. Generate an RSA key pair to be used for signing the edge service with the command below. This command will put the keys into a default location where other CLI commands know where to find them.

```bash
hzn key create -l 4096 testorg joe@everywhere.com
```

The output will be something like this.

```bash
Created keys:
        /root/.hzn/keys/service.private.key
        /root/.hzn/keys/service.public.pem
```

A service is one or more microservices that are deployed on an edge device as containers. A service has to be signed so that the agent can verify that autheticity of the service definition.

2. Publish the service definition to the exchange with the command below. The command will run for a bit, and will pull each container from the container registry.

```bash
hzn exchange service publish -P -f ./patterns/service.json
```

The container digest is recorded in the published service definition. This example service is composed of several EdgeX microservices.

3. Check that the service definition was published and is available in the exchange with the command below.

```bash
hzn exchange service list
```

The expected output is as follows.

```json
[
  "testorg/com.github.joewxboy.horizon.edgex_1.0.1_amd64"
]
```

4. Publish pattern to the exchange. A pattern is the easiest way for a node (agent ?) to indicate which services it should run.

```bash
hzn exchange pattern publish -f ./patterns/pattern.json
```

5. Check pattern is available with the command below.

```bash
hzn exchange pattern list
```

The expected output is as follows.

```bash
[
  "testorg/pattern-edgex-amd64"
]
```
