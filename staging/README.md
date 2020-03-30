# Delivering EdgeX Foundry with Open Horizon

This is a set of instructions to demonstrate a method for using the [Open Horizon](https://github.com/open-horizon) project to deliver and monitor a simple deployment of [EdgeX Foundry](https://wiki.edgexfoundry.org).

- [Delivering EdgeX Foundry with Open Horizon](#delivering-edgex-foundry-with-open-horizon)
  - [Introduction](#introduction)
  - [Background](#background)
  - [Goals](#goals)
    - [Run the Hub and Agent](#run-the-hub-and-agent)
    - [Configure an Organization and Account](#configure-an-organization-and-account)
    - [Register a Service and Deployment Patterrn](#register-a-service-and-deployment-patterrn)
    - [Register an Edge Node](#register-an-edge-node)
  - [High Level Steps](#high-level-steps)
  - [FAQ](#faq)

## Introduction

The Open Horizon and EdgeX Foundry projects have chosen to work together on this integration project, which will be contributed to the EdgeX project and live in their holding code repository.  This effort demonstrates both how the two communities can work together, and how the two solutions bring value to each other in order to create a greater solution.

Open Horizon will demonstrate containerized application orchestration and delivery, as well as Machine Learning asset synchronzation.  EdgeX Foundry will demonstrate southbound device connectivity and data plane management.  There is virtually no overlap between capabilities, with the possible exception of the EdgeX System Management Agent (SMA).  Where there is potential overlap, there is an opportunity to demonstrate how the two solutions can gracefully interoperate.

This integration effort takes place in three phases: Crawl, Walk, and Run.  Each phase has outlined goals and deliverables which were stipulated in advance, but can be altered, if needed.  At the end of the three phases, we will discuss any further actions to be taken.

_Value of integrating Open Horizon and EdgeX Foundry_

## Background

The EdgeX Foundry instance will contain a [Random Integer Device Service](https://docs.edgexfoundry.org/Ch-ExamplesRandomDeviceService.html) that will post a simple random event message every five seconds.

> Caveat Emptor: This document will give you the steps needed to get a small dev instance of the Horizon services running. It will not, and should not, be used to serve edge devices in a production environment. However, it is sufficient to test services.

## Goals

### Run the Hub and Agent

These instructions will show you how to configure and run a single instance of the Open Horizon Hub Services (Exchange, Switchboard, AgBots, Sync Service) within a Virtual Machine (VM) in one logical tier, and the Open Horizon Agent (Anax) in another tier.  While the Agent could be deployed in the same logical tier as the Services, this example keeps it separate so that you can more easily see how to deploy multiple instances of the Agent connecting to a single deployment of the Services.

### Configure an Organization and Account

You will configure an organization and an `admin` enabled user account (into that organization) on the Horizon Exchange. This will give you credentials that will be used in subsequent steps below.  By creating an `admin` account, it will allow you to try out all of the APIs.  By creating a new organization, it will allow you to see how Services, Deployment Patterns, and Policies are all limited to their owning organizations.

### Register a Service and Deployment Patterrn

You will use the Horizon Agent to register the EdgeX micro-services as a single Service.  This will involve creating an asymmetric key pair and using it to sign your service definition while publishing it to the Exchange. Likewise, you will create a Deployment Pattern, publish it, and then register the pattern on the edge node to receive the specified Service.

### Register an Edge Node

You will register the device that is running the Agent as an edge node with the Exchange, then associate the Deployment Pattern with the device to trigger Service deployment, which will stand up the micro-services, monitor them, and keep them running.

## High Level Steps

1. Install Hub on [AWS EC2 Ubuntu Linux](hub/aws-ec2-linux-vm.md). _Note, these instructions should apply for Ubuntu instances on-premises also.
2. Install agent on
   1. [OSX](agent/osx.md)
   2. [Ubuntu](agent/ubuntu.md)
3. [Publish service](verification/hub.md) to Exchange
4. [Register node](verification/agent.md)

## FAQ

TBD
