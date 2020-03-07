# Install Open Horizon Hub Services on AWS EC2

This document describes the installation and configuration of Open Horizon Hub Services (Exchange, Switchboard, AgBots, Sync Service) on an AWS EC2 Ubuntu Linux instance. After following the instructions on this page, you should have an up and running Open Horizon Hub end-point instance.

- [Install Open Horizon Hub Services on AWS EC2](#install-open-horizon-hub-services-on-aws-ec2)
  - [Pre-requisites](#pre-requisites)
  - [Installation](#installation)
  - [Verification](#verification)
  - [Configure](#configure)

## Pre-requisites

The following pre-requisites apply for an AWS EC2 instance.

1. OS: Ubuntu server, latest build recommended.
2. Type: `t2.medium` (4GB RAM and 2vCPU)
3. Storage: 8GB minimum.
4. Network ports: `22`, `3090` and `9443`
5. Root access to the VM is needed to run rest of the instructions.

## Installation

> If you are not already running as root, please become root with `sudo -s` or similar.

1. Install utilities with the command below.

``` bash
apt-get -y update
apt-get -y install apache2-utils jq curl make gcc
```

2. Install Container Engine

``` bash
curl -fsSL get.docker.com | sh
apt-get install -y docker-compose
```

3. Create and persist environment variables.

> The `HZN_EXCHANGE_ROOT_USER_AUTH` saves the password for the `root` Horizon user. Change this password and delete the `bash` history with ....

```bash
export MY_IP=`curl -s httpbin.org/ip | jq -r .origin`
echo "export MY_IP=${MY_IP}" >> $HOME/.bashrc
export HZN_ORG_ID=testorg
echo "export HZN_ORG_ID=testorg" >> $HOME/.bashrc
export HZN_EXCHANGE_URL=http://${MY_IP}:3090/v1/
echo "export HZN_EXCHANGE_URL=http://${MY_IP}:3090/v1/" >> $HOME/.bashrc
export HZN_FSS_CSSURL=http://${MY_IP}:9443
echo "export HZN_FSS_CSSURL=http://${MY_IP}:9443" >> $HOME/.bashrc
export HZN_EXCHANGE_ROOT_USER_AUTH='root/root:Horizon-Rul3s'
echo "export HZN_EXCHANGE_ROOT_USER_AUTH='root/root:Horizon-Rul3s'" >> $HOME/.bashrc
```

4. Clone this repository with `git clone https://github.com/edgexfoundry-holding/open-horizon-integration`

5. Edit the Hub Services configuration JSON

```bash
cd open-horizon-integration/hub/oh
envsubst '${MY_IP}' < config.json.template > config.json
```

You may verify the configuration of various items with the following commands.

| Item                                | Command                                      |
| ----------------------------------- | -------------------------------------------- |
| IP address of the machine           | `jq -r .horizon.hostname config.json`        |
| AgBot name and token                | `jq -r .services.agbot.bot config.json`      |
| Exchange root user                  | `jq -r .exchange.root config.json`           |
| Exchange root password              | `jq -r .exchange.password config.json`       |
| Organization                        | `jq -r .horizon.namespace config.json`       |
| Organization (_also here_)          | `jq -r .exchange.org config.json`            |
| Admin user of your org              | `jq -r .exchange.admin.username config.json` |
| Amdin password of your organisation | `jq -r .exchange.admin.password config.json` |

6. Build and start services

```bash
cd open-horizon-integration/hub/oh
make
make up
```

## Verification

To verify the installation, follow the steps below.

1. Request the endpoints for `version` and `status`

``` bash
curl -u ${HZN_EXCHANGE_ROOT_USER_AUTH} ${HZN_EXCHANGE_URL}admin/version
```

Output:

```bash
2.12.3
```

```bash
curl --silent -u ${HZN_EXCHANGE_ROOT_USER_AUTH} ${HZN_EXCHANGE_URL}admin/status | jq .
```

Output:

```json
{
  "msg": "Exchange server operating normally",
  "numberOfUsers": 1,
  "numberOfNodes": 0,
  "numberOfNodeAgreements": 0,
  "numberOfNodeMsgs": 0,
  "numberOfAgbots": 0,
  "numberOfAgbotAgreements": 0,
  "numberOfAgbotMsgs": 0,
  "dbSchemaVersion": 31
}
```

2. Listing existing organizations

``` bash
curl --silent -u ${HZN_EXCHANGE_ROOT_USER_AUTH} ${HZN_EXCHANGE_URL}orgs | jq .
```

Output:

```json
{
  "orgs": {
    "root": {
      "orgType": "",
      "label": "Root Org",
      "description": "Organization for the root user only",
      "lastUpdated": "2020-03-07T05:26:37.184Z[UTC]",
      "tags": null,
      "heartbeatIntervals": {
        "minInterval": 0,
        "maxInterval": 0,
        "intervalAdjustment": 0
      }
    },
    "IBM": {
      "orgType": "IBM",
      "label": "IBM Org",
      "description": "Organization containing IBM services",
      "lastUpdated": "2020-03-07T05:26:37.220Z[UTC]",
      "tags": null,
      "heartbeatIntervals": {
        "minInterval": 0,
        "maxInterval": 0,
        "intervalAdjustment": 0
      }
    }
  },
  "lastIndex": 0
}
```

3. Add an organization named `testorg`

``` bash
curl -sSf -X POST -u ${HZN_EXCHANGE_ROOT_USER_AUTH} -H "Content-Type:application/json" -d '{"label": "testorg", "description": "Organization for Testing"}' ${HZN_EXCHANGE_URL}orgs/testorg | jq .
```

Output:

```json
{
  "code": "ok",
  "msg": "org 'testorg' created"
}
```

4. List the existing Organizations again to see `testorg` now in the list

``` bash
curl --silent -u ${HZN_EXCHANGE_ROOT_USER_AUTH} ${HZN_EXCHANGE_URL}orgs | jq .
```

Output:

```json
{
  "orgs": {
    "root": {
      "orgType": "",
      "label": "Root Org",
      "description": "Organization for the root user only",
      "lastUpdated": "2020-03-07T05:26:37.184Z[UTC]",
      "tags": null,
      "heartbeatIntervals": {
        "minInterval": 0,
        "maxInterval": 0,
        "intervalAdjustment": 0
      }
    },
    "IBM": {
      "orgType": "IBM",
      "label": "IBM Org",
      "description": "Organization containing IBM services",
      "lastUpdated": "2020-03-07T05:26:37.220Z[UTC]",
      "tags": null,
      "heartbeatIntervals": {
        "minInterval": 0,
        "maxInterval": 0,
        "intervalAdjustment": 0
      }
    },
    "testorg": {
      "orgType": "",
      "label": "testorg",
      "description": "Organization for Testing",
      "lastUpdated": "2020-03-07T05:34:37.957Z[UTC]",
      "tags": null,
      "heartbeatIntervals": {
        "minInterval": 0,
        "maxInterval": 0,
        "intervalAdjustment": 0
      }
    }
  },
  "lastIndex": 0
}
```

## Configure

If no issues were reported while verification, the Hub can be configured with an `admin` user for `testorg`. Further, a new AgBot, `agBot1` is configured to listen for Deployment Patterns and Policies from the `testorg` Organization. To apply, this configuration, run the following command.

``` bash
cd open-horizon-integration/hub/oh
make prime
```

Output

```bash
export \
  EXCHANGE_AGBOT_NAME=agbot1 \
  EXCHANGE_AGBOT_TOKEN=agbot1pw \
  EXCHANGE_URL=http://54.xxx.xxx.xxx:3090/v1 \
  EXCHANGE_ROOT=root/root \
  EXCHANGE_PASSWORD=Horizon-Rul3s \
  EXCHANGE_ORGANIZATION=testorg \
  EXCHANGE_ADMIN_USERNAME=admin \
  EXCHANGE_ADMIN_PASSWORD=adminpw \
  && ./prime-exchange.sh
Reseting database
exchange_db_reset: dropdb: db deleted successfully
exchange_db_reset: initializedb: db initialized successfully
exchange_create_org: creating org: testorg; type: null; result: org 'testorg' created
exchange_create_user: creating user: admin in org: testorg; token: adminpw; type: null; result: 1 user added successfully
exchange_register_agbot: registering agbot: agbot1; type: null; result: agbot added or updated
exchange_register_allpattern: configuring agbot to serve patterns for org testorg: agbot1; type: null; result: pattern testorg_*_testorg added
exchange_register_allpolicy: configuring agbot to serve policies for org testorg: agbot1; type: null; result: businessPol testorg_*_testorg added
```

To verify the configuration, run the following command.

``` bash
curl --silent -sSf -u ${HZN_EXCHANGE_ROOT_USER_AUTH} ${HZN_EXCHANGE_URL}orgs/testorg/users | jq .
```

Output:

```json
{
  "users": {
    "testorg/admin": {
      "password": "$2a$10$68X34kADxWmPp.ZdMiHDseISfdtBJqOyiPugfeBJiz.O0uXC2HHKa",
      "admin": true,
      "email": "admin@testorg",
      "lastUpdated": "2020-03-07T05:35:42.184Z[UTC]",
      "updatedBy": "root/root"
    }
  },
  "lastIndex": 0
}
```
