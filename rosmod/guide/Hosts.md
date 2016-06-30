---
layout: page
title: "Hosts"
description: ""
---
{% include JB/setup %}

# Hosts

Hosts can have two types of children: `Interfaces` and `Capabilities`.  The attributes of `Hosts` are described in the section regarding `System` models.

## Interfaces

Interfaces are used to specify the network interfaces available on the host.  Each interface only has a **name**, e.g. `eth0`, specifying the network interface as it would be seen on the actual hardware, for instance by running `ifconfig` in _linux_.

## Capabilities

Capabilities are the corresponding object to `Constraints`, which are contained by `Components`.  A `Capability` corresponds to some provision that the `Host` has that the software may need, for instance `hasCamera`, or `hasGPIO`.  Components which have the correspondingly named `Constraint` can only be mapped during deployment/execution to hosts which have `Capabilities` of the same name.  Note that `Capabilities` need not be unique within or between hosts.

## Users

Hosts have a _Set_ of `Users` which specify the users which have valid credentials on this host.  The set of users is viewed and edited by selecting the `SetEditor` visualizer from within a _Host_.  You specify that a user can access a host by dragging the user object (which exists in the parent system model) from the tree browser into the `Users` _SetEditor_ of the relevant host.