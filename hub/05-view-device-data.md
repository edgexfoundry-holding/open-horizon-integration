

# View the Device Data

Now that EdgeX Foundry (EXF) is running on your Edge Node and the [Random Integer Device Service](https://docs.edgexfoundry.org/1.2/examples/Ch-ExamplesRandomDeviceService/) is posting a simple random event message every five seconds, let's explore ways to view that data.  We'll then modify the interval and range of values and see it change in reponse.

## Ensure everything is functioning normally

Let's open a shell to the environment that is running the Horizon Agent (Anax).  To confirm that it is the correct location and Horizon is operating properly, check the version:

``` bash
hzn version
```

It should respond with something similar to this:

``` bash
Horizon CLI version: 2.25.0
Horizon Agent version: 2.25.0
```

Check to ensure that your Edge Node is properly configured:

``` bash
hzn node list | jq '.configstate'
```

You should see output similar to this:

``` bash
{
  "state": "configured",
  "last_update_time": "2020-04-04 07:37:39 -0700 PDT"
}
```

Then check to ensure that the `com.github.joewxboy.horizon.edgex` Service is running:

``` bash
hzn service list
```

You should see output like this:

``` json
[
  {
    "url": "com.github.joewxboy.horizon.edgex",
    "org": "mycluster",
    "version": "1.0.1",
    "arch": "amd64",
    "variables": {}
  }
]
```

And last, let's ask Docker to show us the running images contained within that Service:

``` bash 
docker ps
```

Which should return something like this:

``` bash
CONTAINER ID        IMAGE                                    COMMAND                  CREATED             STATUS              PORTS                                              NAMES
4b764ec9140c        edgexfoundry/docker-core-metadata-go     "/core-metadata --re…"   6 hours ago         Up 6 hours          0.0.0.0:48081->48081/tcp, 48082/tcp                1a85b4ce42a70ca27b0d1eee80c154e56ca8dda50f41199ccb4ec5e253a9c1cd-edgex-core-metadata
1c1b59fe238c        edgexfoundry/docker-core-data-go         "/core-data --regist…"   6 hours ago         Up 6 hours          0.0.0.0:5563->5563/tcp, 0.0.0.0:48080->48080/tcp   1a85b4ce42a70ca27b0d1eee80c154e56ca8dda50f41199ccb4ec5e253a9c1cd-edgex-core-data
d88ec30ecb59        edgexfoundry/docker-core-command-go      "/core-command --reg…"   6 hours ago         Up 6 hours          0.0.0.0:48082->48082/tcp                           1a85b4ce42a70ca27b0d1eee80c154e56ca8dda50f41199ccb4ec5e253a9c1cd-edgex-core-command
8803a65bb1de        edgexfoundry/docker-support-logging-go   "/support-logging --…"   6 hours ago         Up 6 hours          0.0.0.0:48061->48061/tcp                           1a85b4ce42a70ca27b0d1eee80c154e56ca8dda50f41199ccb4ec5e253a9c1cd-edgex-support-logging
e3c0775618c7        edgexfoundry/docker-edgex-mongo          "docker-entrypoint.s…"   6 hours ago         Up 6 hours          0.0.0.0:27017->27017/tcp                           1a85b4ce42a70ca27b0d1eee80c154e56ca8dda50f41199ccb4ec5e253a9c1cd-edgex-mongo
af4e5e061f49        edgexfoundry/docker-edgex-volume         "/bin/sh -c '/usr/bi…"   6 hours ago         Up 6 hours                                                             1a85b4ce42a70ca27b0d1eee80c154e56ca8dda50f41199ccb4ec5e253a9c1cd-edgex-files
a9517da05d16        edgexfoundry/docker-device-random-go     "/device-random --pr…"   6 hours ago         Up 6 hours          0.0.0.0:49988->49988/tcp                           1a85b4ce42a70ca27b0d1eee80c154e56ca8dda50f41199ccb4ec5e253a9c1cd-edgex-device-random
```

Verify that you see seven images, and that CREATED and STATUS columns show that none of them are restarting.

## View the data

Now that you've verified that Open Horizon has deployed EXF, it's time to use EXF to view the events and readings that the Random Integer Device Service is sending.

According to the EXF documentation, you could request the URL for Core Data, but that will show you all of the events and readings since the Service was started, which could get quite long:

``` bash
curl --silent http://localhost:48080/api/v1/event | jq
```

We can further limit the results with `jq` to show us only the most recent reading:

``` bash
curl --silent http://localhost:48080/api/v1/event | jq .[0].readings[0].value
```

Core Metadata will show us the properties of the Device:

``` bash
curl --silent http://localhost:48081/api/v1/device | jq .[0].service
```

## Modify the Device Settings

The initial configuration of the Device is specified in the [Device Random Configuration TOML file](./hub/res/device-random-config.toml).  Properties within can be divided into those that can be updated without restarting the service (writable) and those that will not take effect until the Service is restarted (read-only).  See [Configuration and Registry](https://docs.edgexfoundry.org/1.2/microservices/configuration/Ch-Configuration/#readable-vs-writable-settings) for more information on this topic. 

### Writable Settings

The Core Command provides a PUT endpoint that will allow you to modify a Device configuration to alter future reading values.

### Read-only Settings

Persisted settings are stored in Consul in a typical EdgeX Foundry installation, and should be modified there.  EdgeX provides several methods to update those values.  In this case, we are using TOML files to configure services, and thus you will need to update the [Device Random Configuration TOML file](./hub/res/device-random-config.toml) to change those value.  A restart of the Service will then be required for any changes to take effect.

### Restarting an EXF Service

If you had the System Management Agent (SMA) installed you could simply POST a restart action to the appropriate endpoint ([restart a service](https://docs.edgexfoundry.org/1.2/microservices/system-management/agent/Ch_SysMgmtAgent/#restart-a-service)) to initiate a graceful restart of that specific micro-service.  However, we do not have the SMA in our current example.

You could use Open Horizon to restart the Service, but that would introduce two complications.  First, we populated the configuration from a static, external file.  For the settings to persist, we'd need to edit that file before restarting.  Second, we defined the EXF orchestration as a single Horizon Service, which means that you'd have to restart the whole instance of seven Docker images.

Or you could `ssh` to the machine and use the Docker CLI to kill the process and rely on Open Horizon to restart it for you automatically.  While this approach works well, it is not graceful, does not solve the persistence issue, and violates the spirit of Open Horizon in that we're attempting to prevent an administrator from needing to log onto individual Devices in the first place.

Potential ways forward include refactoring the example so that individual EXF micro-services are implemented as individual Open Horizon Services, or adding the SMA to the Open Horizon Service definition.

# Next

[Add the SMA to the Service Definition](06-add-the-sma.md).
