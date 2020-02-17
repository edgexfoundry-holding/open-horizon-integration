# Delivering EdgeX Foundry with Open Horizon

This is a set of instructions to demonstrate one method for using the 
[Open Horizon](https://github.com/open-horizon) project to 
deliver and monitor a simple deployment of [EdgeX Foundry](https://wiki.edgexfoundry.org).

The EdgeX Foundry instance will contain a 
[Random Integer Device Service](https://docs.edgexfoundry.org/Ch-ExamplesRandomDeviceService.html) 
that will post a simple random event message every five seconds.

Caveat Emptor: This document will give you the steps needed to get a small dev instance of the Horizon services running. 
It will not, and should not, be used to serve edge devices in a production environment. 
However, it is sufficient to test services.

## Goals

### Run the Services and Agent

These instructions will show you how to configure and run a single instance of the Open Horizon Hub Services (Exchange, Switchboard, AgBots, Sync Service) within a Virtual Machine (VM) in one logical tier, 
and the Open Horizon Agent (Anax) in another tier.  While the Agent could be deployed in the same logical tier as the Services, this example keeps it separate so that you can more easily see how to deploy multiple instances of the Agent connecting to a single deployment of the Services.  

### Configure an Organization and Account

You will configure an organization and an admin-enabled user account (into that organization) on the Horizon Exchange.  This will give you credentials that will be used in subsequent steps below.  By creating an admin account, it will allow you to try out all of the APIs.  By creating a new orgnaization, it will allow you to see how Services, Deployment Patterns, and Policies are all limited to their owning organizations.

### Register a Service and Deployment Pattern, Policies

You will use the Horizon Agent to register the EdgeX micro-services as a single Service.  This will involve creating an asymmetric key pair and using it to sign your service definition while publishing it to the Exchange. 
Likewise, you will create a Deployment Pattern, publish it, and then register the pattern on the edge node to receive the specified Service.  Last, you can create Policies (Node, Business, and Service) and associate those in order to see the Service automatically deployed to the edge node. 

### Register an Edge Node

You will register the device that is running the Agent as an edge node with the Exchange, 
then associate the Deployment Pattern with the device to trigger Service deployment, 
which will stand up the micro-services, monitor them, and keep them running.

## High Level Steps

+ [Install the Open Horizon Services](01-horizon-services-setup.md)
+ [Build and Run](02-build-and-run-horizon.md) the Open Horizon Services
+ [Install the Open Horizon Agent](03-install-agent.md) software
+ [Configure the Agent](04-configure-anax.md)

## FAQ

TBD