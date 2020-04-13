# Add the SMA to the Service Definition

This continues the instructions from [Configure the Agent](04-configure-anax.md) and [View the Device Data](05-view-device-data.md).  Our goal is to take the existing Open Horizon Service and make any needed changes in order to introduce a new EdgeX Foundry Service into the existing deployed configuration.

You always want to validate what you're doing to ensure that it is actually possible (and as simple as it appears to be).  First, see if the SMA is like all of the other services that we're already using.  Looking in the [EdgeX Golang Services Monorepo](https://github.com/edgexfoundry/edgex-go) shows that the [cmd folder](https://github.com/edgexfoundry/edgex-go/tree/master/cmd) contains the services that we are using, and that `sys-mgmt-agent` is a peer of the existing services that we are using.

Second, looking at the version numbers of the services in our [services.json file](./configs/service.json) shows that most are at version 1.0.1, yet [the current fuji release](https://github.com/edgexfoundry/developer-scripts/blob/master/releases/fuji/compose-files/docker-compose-redis-fuji-no-secty.yml) is around version 1.1.0.  Let's use the [Edinburgh compose files](https://raw.githubusercontent.com/edgexfoundry/developer-scripts/master/releases/edinburgh/compose-files/docker-compose-edinburgh-no-secty-1.0.1.yml) as our template to start from since those versions align exactly with what we already have. _Note: Let's upgrade to Geneva after it is released._

Third, let's look at the SMA's YAML snippet to see if there are any configured items that our existing service definition does not already take into account.

``` yaml
  system:
    image: edgexfoundry/docker-sys-mgmt-agent-go:1.0.1
    ports:
      - "48090:48090"
    container_name: edgex-sys-mgmt-agent
    hostname: edgex-sys-mgmt-agent
    networks:
      edgex-network:
        aliases:
            - edgex-sys-mgmt-agent
    volumes:
      - db-data:/data/db
      - log-data:/edgex/logs
      - consul-config:/consul/config
      - consul-data:/consul/data
      - /var/run/docker.sock:/var/run/docker.sock
    depends_on:
      - logging
```

The `container_name` property is the OH key.  The `image` looks fine, and maps directly to the OH `image` property.  The `ports` will need to be added to the OH `specific_ports` property. The `networks` property will be skipped because it is implicity supported.  Since all of the EXF Services are on the same network, and are all included in the single OH Service, they are by default all on the same network.  The `depends_on` property will not be implemented in OH since it is not an _external_ Service dependency.  Which takes us to the `volumes` property.  The first four values are already used by the other EXF Services, so we can copy over their values to the `binds` property.  The `docker.sock` reference we'll add, and confirm if OH needs read-only or read-write access.  This leaves us with this OH JSON snippet:

``` json
        "edgex-sys-mgmt-agent": {
            "binds": [
                "db-data:/data/db",
                "log-data:/edgex/logs:rw",
                "consul-config:/consul/config",
                "consul-data:/consul/data",
                "/var/run/docker.sock:/var/run/docker.sock:rw",
                "/root/res/sma-config.toml:/res/docker/sma-config.toml:ro"
            ],
            "command": [
                "--registry=false"
            ],
            "specific_ports": [
                { "HostPort": "48090/tcp", "HostIP": "0.0.0.0" }
            ],
            "image": "edgexfoundry/docker-sys-mgmt-agent-go:1.0.1",
            "privileged": true
        },
```

Fourth, we need to create a configuration TOML file for the EXF SMA Service.  Since we're using the Edinburgh Release version, let's [consult that branch](https://raw.githubusercontent.com/edgexfoundry/edgex-go/edinburgh/cmd/sys-mgmt-agent/res/configuration.toml) to look at the default copy.  It looks like we'll need to add a proper value for the `ExecutorPath` property, and slightly adjust the path for the Logging `File` property.  We'll also want to remove any Clients that we don't have in the OH Service.  We'll name this file `sma-config.toml` and place it in the `res` folder and ensure it's referenced in the `binds` section of the OH `service.json` configuration.

Fifth and last, we'll save the updated service definition file in `./configs/sma-service.json`.  That should complete the configuration steps.  Next up, let's test and then publish.

## Test and Publish


-----

## The End
