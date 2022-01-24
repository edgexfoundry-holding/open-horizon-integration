# open-horizon-integration

## Introduction

This repo was created to demonstrate one possible way to integrate the EdgeX Foundry solution with the Open Horizon project by having Open Horizon deliver and monitor an EdgeX Foundry instance.  This was initially accomplished in February, 2020.  Since that time, the repo has receive minor updates and bug fixes, but no new development.

As of January 2022, people are using the example in this repo as a pattern to emulate in their own deployments, so the Open Horizon project will be planning some updates to implement the newest code bases from both projects, and to follow the latest Best Practices.  Please watch this repo to be kept updated on all changes, and star the repo if you find it useful or interesting.

## Overview

For a simple development environment, installation is comprised of three basic steps:

1. Installing the Open Horizon Management Hub services on an x86 machine or VM
2. Installing the Open Horizon Agent on an x86, RISC-V, armv6-v8 or arm64, or PPCle64 architecture machine running Ubuntu, Fedora, macOS, or Raspberry Pi OS (or Kubernetes for the Cluster Agent)
3. Creating and publishing service and deployment policies to the Hub's Exchange

This page will walk you through those basic steps as simply as possible.  The Open Horizon all-in-one script will be used for steps 1 and 3, and the agent-install script for step 2.  The description will also point out helpful tips to ensure a successful installation experience.  If you have any questions, please join Open Horizon's Documentation WG chat room at https://chat.lfx.linuxfoundation.org/

## Install Management Hub services

NOTE: The Management Hub can be installed on either a standard x86 workstation with the OS already installed, or in a VM, or a Kubernetes cluster.  For the purposes of these simplified instructions, we'll use a standalone x86 machine or VM.  If you're using a VM, please [configure the network within the VM](https://www.youtube.com/watch?v=YQqFnRNL98s) to operate in bridged mode so that the VM instance has a unique IP address separate from the host machine.

1. Boot the machine or VM and log in.  Become "root" user with `sudo -i`.
2. Update the OS package manager and install pre-requisites.  On macOS, "brew" is recommended.  For Ubuntu, that would be:
``` shell
apt-get -y update
apt-get -y install gcc make curl jq net-tools
```
3. Get the machine or VM's public IP address.  Use `ifconfig` to determine what it is.
4. Set an environment variable to that address.  Ex.  if your IP address is 192.168.34.233, then:
``` shell
export HZN_LISTEN_IP=192.168.34.233
```
5. Install the "all-in-one" script.  Read and follow [the detailed instructions](https://github.com/open-horizon/devops/tree/master/mgmt-hub), or just run the line below:
``` shell
curl -sSL https://raw.githubusercontent.com/open-horizon/devops/master/mgmt-hub/deploy-mgmt-hub.sh | bash
```
6. Save the credentials shown at the end of a successful installation process. You will need these later.
7. Set the following environment variables to connect to the Exchange and to allow publishing. Replace the `<values>` with the actual value indicated. Also add them to your shell's RC file if you'd like them to persist beyond this session.  You may also want to use these for each Agent instance you install on other machines or VMs.
``` shell
export HZN_ORG_ID=myorg
export HZN_EXCHANGE_USER_AUTH=admin:<insert value from installation credentials>
export HZN_DEVICE_TOKEN=<insert value from installation credentials or use random string>
export HZN_DEVICE_ID=node1
export HZN_EXCHANGE_URL=http://<local-IP-address-here>:3090/v1/
export HZN_FSS_CSSURL=http://<local-IP-address-here>:9443/
```
8. Exit the `root` shell back to your initial login session.  You may want to export the above variables and persist them in this session as well.

## Install Agent

These instructions are meant to be run on a machine or device that is physically separate from the Management Hub.  These simplified instructions also assume you are on the same local network as the Hub, or that its IP address is routable from this device or machine.  The steps will connect directly to the Hub's Sync Service to download and install an appropriate Agent for this machine or device with proper initial configuration.

1. Log into your machine or device and become "root" user: `sudo -i`
2. Follow step 2 of the Hub installation instructions to install pre-requisites
3. Follow step 7 of the Hub installation instructions to set environment variables
4. Download and run the Agent installation script:
``` shell
curl -sSL https://github.com/open-horizon/anax/releases/latest/download/agent-install.sh | sudo -s -E bash -s -- -i anax: -k css: -c css: -p IBM/pattern-ibm.helloworld -w '*' -T 120
```
5. Exit the `root` shell back to your initial login session.  You may want to export the above variables and persist them in this session as well.

## Monitoring, modifying, unregistering, and re-registering services

TBD

## For more information ...

Visit [the Open Horizon documentation web site](https://open-horizon.githob.io/).