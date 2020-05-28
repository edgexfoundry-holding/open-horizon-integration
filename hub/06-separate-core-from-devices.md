# Separate EdgeX Foundry Core from Devices

First, let's go to the `configs` folder and stub out the core and device services:

``` bash
cd ~/open-horizon-integration/hub/configs
```

NOTE: this assumes that the git repo you started with was cloned into your home directory (`~/`). Please adjust accordingly.

Then, let's create separate folders for the `core` and `device` services, and stub them out:

``` bash
mkdir core && cd $_
hzn dev service new
cd ..
mkdir devices && cd $_
hzn dev service new
```

This will create a folder structure with the following files and subdirectories:

```
core
> horizon
  > dependencies
    .gitignore
  .gitignore
  hzn.json
  pattern-all-arches.json
  pattern.json
  service.definition.json
  service.policy.json
  userinput.json
```

## Create the `core` Service

Let's first fill out the `core` Service Definition file.  Go to the appropriate folder and edit `service.definition.json`:

``` bash
cd ../core/horizon
vi service.definition.json
```

Let's fill in the appropriate values:

|| Property | Value
| description | EdgeX Foundry service without devices
| sharable | singleton
| userInput | ```      { "name": "EXPORT_DISTRO_CLIENT_HOST", "label": "", "type": "string", "defaultValue": "export-client" },
      { "name": "EXPORT_DISTRO_DATA_HOST", "label": "", "type": "string", "defaultValue": "edgex-core-data" },
      { "name": "EXPORT_DISTRO_CONSUL_HOST", "label": "", "type": "string", "defaultValue": "edgex-config-seed" },
      { "name": "EXPORT_DISTRO_MQTTS_CERT_FILE", "label": "", "type": "string", "defaultValue": "none" },
      { "name": "EXPORT_DISTRO_MQTTS_KEY_FILE", "label": "", "type": "string", "defaultValue": "none" },
      { "name": "LOG_LEVEL", "label": "logging level", "type": "string", "defaultValue": "info" },
      { "name": "LOGTO", "label": "where to log", "type": "string", "defaultValue": "." }
```
| deployment > services | ```
        "edgex-files": {
          "binds": [
            "db-data:/data/db",
            "log-data:/edgex/logs:rw",
            "consul-config:/consul/config",
            "consul-data:/consul/data"
          ],
          "image": "edgexfoundry/docker-edgex-volume:1.0.0",
          "privileged": true
        },
        "edgex-mongo": {
          "binds": [
            "db-data:/data/db",
            "log-data:/edgex/logs:rw",
            "consul-config:/consul/config",
            "consul-data:/consul/data"
          ],
          "specific_ports": [
            { "HostPort": "27017/tcp", "HostIP": "0.0.0.0" }
          ],
          "image": "edgexfoundry/docker-edgex-mongo:1.0.1",
          "privileged": true
        },
        "edgex-support-logging": {
          "binds": [
            "db-data:/data/db",
            "log-data:/edgex/logs:rw",
            "consul-config:/consul/config",
            "consul-data:/consul/data",
            "/root/res/logging-config.toml:/res/docker/configuration.toml:ro"
          ],
          "command": [
            "--registry=false"
          ],
          "specific_ports": [
            { "HostPort": "48061/tcp", "HostIP": "0.0.0.0" }
          ],
          "image": "edgexfoundry/docker-support-logging-go:1.0.1",
          "privileged": true
        },
        "edgex-core-metadata": {
          "binds": [
            "db-data:/data/db",
            "log-data:/edgex/logs:rw",
            "consul-config:/consul/config",
            "consul-data:/consul/data",
            "/root/res/metadata-config.toml:/res/docker/configuration.toml:ro"
          ],
          "command": [
            "--registry=false"
          ],
          "specific_ports": [
            { "HostPort": "48081/tcp", "HostIP": "0.0.0.0" }
          ],
          "image": "edgexfoundry/docker-core-metadata-go:1.0.1",
          "privileged": true
        },
        "edgex-core-data": {
          "binds": [
            "db-data:/data/db",
            "log-data:/edgex/logs:rw",
            "consul-config:/consul/config",
            "consul-data:/consul/data",
            "/root/res/data-config.toml:/res/docker/configuration.toml:ro"
          ],
          "command": [
            "--registry=false"
          ],
          "specific_ports": [
            { "HostPort": "48080/tcp", "HostIP": "0.0.0.0" },
            { "HostPort": "5563/tcp", "HostIP": "0.0.0.0" }
          ],
          "image": "edgexfoundry/docker-core-data-go:1.0.1",
          "privileged": true
        },
        "edgex-core-command": {
          "binds": [
            "db-data:/data/db",
            "log-data:/edgex/logs:rw",
            "consul-config:/consul/config",
            "consul-data:/consul/data",
            "/root/res/command-config.toml:/res/docker/configuration.toml:ro"
          ],
          "command": [
            "--registry=false"
          ],
          "specific_ports": [
            { "HostPort": "48082/tcp", "HostIP": "0.0.0.0" }
          ],
          "image": "edgexfoundry/docker-core-command-go:1.0.1",
          "privileged": true
        }
```

Then let's navigate back to the `core` subdirectory and validate the Service Definition file:

``` bash
cd ..
export SERVICE_NAME="com.github.joewxboy.horizon.edgex.core"
export SERVICE_VERSION="1.0.2"
hzn dev service verify
```

If all goes well and there were no typos, you should see output similar to the following:

```
Service project open-horizon-integration/hub/configs/core/horizon verified.
```

Then let's start the service to confirm that it works before publishing to the exchange:

``` bash
docker pull edgexfoundry/docker-edgex-volume:1.0.0
docker pull edgexfoundry/docker-edgex-mongo:1.0.1
docker pull edgexfoundry/docker-support-logging-go:1.0.1
docker pull edgexfoundry/docker-core-metadata-go:1.0.1
docker pull edgexfoundry/docker-core-data-go:1.0.1
docker pull edgexfoundry/docker-core-command-go:1.0.1

docker volume create db-data
docker volume create log-data
docker volume create consul-data
docker volume create consul-config

mkdir -p /var/run/edgex/logs
mkdir -p /var/run/edgex/data
mkdir -p /var/run/edgex/consul/data
mkdir -p /var/run/edgex/consul/config
mkdir -p /root/res
chmod -R a+rwx /var/run/edgex
chmod -R a+rwx /root/res
# copy the files from res to /root/res

hzn dev service start
```

NOTE: If something goes wrong and you need to debug, try adding `-v` flag to the end of `hzn dev service start` to get more debugging information.

NOTE: If you are running on OSX, you may need to substitute a different path for `/root/res`, in which case you should modify the bind paths in the Service Definition files to point to the actual location.

Once you confirm that it runs properly, stop the service:

``` bash
hzn dev service stop
```

## Publish the `core` Service

Set your environment variables so you can connect to the exchange:

NOTE: Replace x.x.x.x with the actual IP address of the machine running the Open Horizon Hub Services.

``` bash
export HZN_EXCHANGE_URL=http://x.x.x.x:3090/v1
export ORG_ID=testorg
export HZN_ORG_ID=testorg
export HZN_EXCHANGE_USER_AUTH=admin:adminpw
echo "export HZN_EXCHANGE_USER_AUTH='admin:adminpw'" >> ~/.bashrc
hzn exchange user list
```

You should get a response like this, if you can connect to the exchange:

``` json
{
  "testorg/admin": {
    "admin": true,
    "email": "admin@testorg",
    "lastUpdated": "2020-05-16T21:26:49.460Z[UTC]",
    "password": "********",
    "updatedBy": "root/root"
  }
}
```

Next, generate an RSA key pair:

``` bash
hzn key create -l 4096 testorg joe@everywhere.com
```

Then publish the `core` service to the exchange:

``` bash
hzn exchange service publish --json-file=horizon/service.definition.json -P
```

Then verify with:

``` bash
hzn exchange service list
```

## Create the `device` Service

Then, let's change to the device service directory and edit the device Service Definition file:

``` bash
cd ../device/horizon
vi service.definition.json
```

Set the following properties:

|| Property | Value
| description | EdgeX Foundry device random service
| sharable | singleton
| requiredServices | ```
      {
        "url": "com.github.joewxboy.horizon.edgex.core",
        "org": "testorg",
        "versionRange": "[1.0.2,INFINITY)",
        "arch": "amd64"
      }
```
| userInput | ```
      { "name": "EXPORT_DISTRO_CLIENT_HOST", "label": "", "type": "string", "defaultValue": "export-client" },
      { "name": "EXPORT_DISTRO_DATA_HOST", "label": "", "type": "string", "defaultValue": "edgex-core-data" },
      { "name": "EXPORT_DISTRO_CONSUL_HOST", "label": "", "type": "string", "defaultValue": "edgex-config-seed" },
      { "name": "EXPORT_DISTRO_MQTTS_CERT_FILE", "label": "", "type": "string", "defaultValue": "none" },
      { "name": "EXPORT_DISTRO_MQTTS_KEY_FILE", "label": "", "type": "string", "defaultValue": "none" },
      { "name": "LOG_LEVEL", "label": "logging level", "type": "string", "defaultValue": "info" },
      { "name": "LOGTO", "label": "where to log", "type": "string", "defaultValue": "." }
```
| deployment > services > $SERVICE_NAME | ```
                "binds": [
                    "db-data:/data/db",
                    "log-data:/edgex/logs:rw",
                    "consul-config:/consul/config",
                    "consul-data:/consul/data",
                    "/root/res/device-random-config.toml:/res/configuration.toml:ro",
                    "/root/res/device-random.yaml:/res/device-random.yaml:ro"
                ],
                "specific_ports": [
                    { "HostPort": "49988/tcp", "HostIP": "0.0.0.0" }
                ],
                "image": "edgexfoundry/docker-device-random-go:1.0.0",
                "privileged": true
```

Then let's navigate back to the `device` subdirectory and validate the Service Definition file:

``` bash
cd ..
export SERVICE_NAME="com.github.joewxboy.horizon.edgex.device"
export SERVICE_VERSION="1.0.2"
hzn dev service verify
```

If all goes well and there were no typos, you should see output similar to the following:

```
New dependency created: url: com.github.joewxboy.horizon.edgex.core, org: testorg, version: [1.0.2,INFINITY), arch: amd64 .
Service project /Users/josephpearson/dev/open-horizon-integration/hub/configs/device/horizon verified.
```

Then let's start the service to confirm that it works before publishing to the exchange:

``` bash
docker pull edgexfoundry/docker-device-random-go:1.0.0

hzn dev service start
```

Once you confirm that it runs properly, stop the service:

``` bash
hzn dev service stop
```

## Publish the `device` Service

Publish the `device` service to the exchange:

``` bash
hzn exchange service publish --json-file=horizon/service.definition.json -P
```

Then verify with:

``` bash
hzn exchange service list
```

You should now see:

``` json
[
  "testorg/com.github.joewxboy.horizon.edgex.device_1.0.2_amd64",
  "testorg/com.github.joewxboy.horizon.edgex.core_1.0.2_amd64"
]
```

## Consume the Services with a Pattern

You should be able to use the default `pattern.json` file in the `horizon` subdirectory.  Publish that to the exchange:

``` bash
hzn exchange pattern publish -f horizon/pattern.json
```

The exchange should respond with:

```
Creating pattern-com.github.joewxboy.horizon.edgex.device-amd64 in the exchange...
```

And then confirm that it's available:

``` bash
hzn exchange pattern list
```

And you should see it:

``` json
[
  "testorg/pattern-com.github.joewxboy.horizon.edgex.device-amd64"
]
```

## End
