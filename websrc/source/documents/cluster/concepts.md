---
layout: "documents"
page_title: "Cluster"
sidebar_current: "cluster-concepts"
description: |-
  Cluster
---


##Definitions
To better understand Contiv Cluster, you should know a few system definitions.

###Node
A physical or virtual server with predefined/discovered compute, memory, storage
and networking capabilities.

###Cluster
A collection of one or more nodes running clustering software such as Docker Swarm.
Nodes may or may not be homogeneous in their capabilities.

###Image
An operating system with a *minimal set* of pre-installed packages used by a node. For instance,
node automated discovery requires a cluster membership service like [Serf](https://www.serfdom.io/)
to be pre-installed and started on the node's operating system during bootstrapping.

###Bootstrap

**Node Bootstrap**: The process of installing the node image and booting the node for the first time.

**Cluster Bootstrap**: The process of adding one or more bootstrapped nodes to the cluster for the first time.

###Lifecycle
A collection of well-defined states that a node or cluster transitions through based on system events.

###Infra Service
A userspace application or a kernel module that runs on a node and provides a support service to
the clustering system software. Examples include plugins, drivers, or components for networking
and storage, key/value stores, etc..

##Features

Contiv Cluster provides the following features. Not all features are implemented, or will be
implemented in the initial release. Please review the [Roadmap](management/ROADMAP.md) or
continue reading to understand individual feature status.
- Node discovery to simplify cluster bootstrapping and expansion
- Cluster management
  - [Inventory](#inventory)
    - Discovery
    - Node state database
  - [Node image management](#node-image-management)
    - Install
    - Upgrade
  - [Node configuration management](#node-configuration-management)
    - Clustering software components (swarm-agent, etc)
    - Infrastructure software components (ovs, serf, consul/etcd, etc)
    - Contiv infrastructure services (netmaster/netplugin and volmaster/volplugin)
  - [Bootstrap](#bootstrap-1)
    - Node
    - Cluster
  - [Lifecycle](#lifecycle-1)
    - Node
    - Cluster

The rest of the the document details the **cluster management** features that were
briefly listed above. Use the [Design Guide](management/DESIGN.md) to better understand the
technical details of Contiv Cluster or use the [Installation and Operations Guide](management/README.md)
to start using the system.

##Inventory
Inventory management provides:

- **Cluster Membership Management**: Automatically discovers nodes and tracks discovery status.
- **Node State Database**: Tracks the current state of the node within the cluster.
Node states consist of the following:

  - **New**: A node that has not completed the bootstrap and discovery process.
  - **Discovered**: A node has completed bootstrapping and notifies the Cluster Manager. The node waits to be Commissioned.
  - **Commissioned**: A node has been configured to participate in the cluster.
  - **Upgraded**: One or more node components have been successfully upgraded and the node is ready to participate in the cluster.
  - **Decommissioned**: All configuration and software components have been removed and the node no longer participates in the cluster.

##Node Image Management
Node Image Management provides:
- **Image Repository**: A central location reachable by nodes, where images are hosted. Images are delivered to nodes using mechanisms such as PXE.
- **Image Installation**: Automates the image installation of new nodes.
- **Upgrades**: Automates the process of upgrading the node image. Upgrades can be automatically triggered and can be performed cluster-wide or rolling.

**Note:** Node Image Management features are **not** included in the initial release of Contiv Cluster.
Operators are responsible for node bootstrapping, including image provisioning.

##Node Configuration Management
Node Configuration Management provides:

- **Configuration Repository**: A central location for hosting node configuration files.
The configuration is used to automate the deployment of nodes.
- **Configuration Push**: The configuration of nodes is pushed from Cluster Manager to nodes.
- **Configuration Cleanup**: Configuration files, service, packages, etc. are removed from nodes.
- **Configuration Verification**: Checks to ensure that the node configuration is truly functional.
- **Configuration Upgrade**: Automates the process of upgrading node software components.
Upgrades can be automatically triggered and can be performed cluster-wide or rolling.
- **Role/Group-Based Configuration**: Nodes can be assigned a group or role.
Role/Group-Based configuration selectively manages services on nodes statically by the
operator or dynamically based on service availability policy.

**Note:** Dynamic role assignment is **not** supported in initial release of Contiv Cluster.

##Bootstrap
Bootstrap involves provisioning a node or cluster for the first time.

###Node
Contiv Cluster provides the following node bootstrapping capabilities:

- Installing the base image
- Performing initial configurations such as disk partitioning
- Assigning an IP address
- Configuring user credentials and permissions to perform configuration management tasks
- Starting infrastructure services such as Serf

**Note:** To start simple this feature is **not** provided as part of initial release of Contiv Cluster.

Operators are responsible for node bootstrapping, including image provisioning.

###Cluster
The cluster bootstrap installs and configures clustering software components such as swarm-master
(for Docker Swarm) to the first bootstrapped node with parameters such as:

- Configuration management parameters such as user information, configuration repository
- Inventory management parameters such as database url

##Lifecycle
Lifecycle management involves integrating multiple cluster and node management tasks at a
central location to simplify monitoring and administration of the cluster.

###Node
Node lifecycle management provides:

- **Bootstrap**: Remotely provision a node's image from a central location. *As described
above this feature is not provided in initial release of the Contiv Cluster.*
- **Cluster Membership**: Automatically track cluster's membership of reachable and unreachable nodes.
- **Commission**: Remotely provision a node's clustering software, configuration, etc..
Optionally commission newly discovered nodes automatically.
- **Upgrade**: Remotely upgrade the image or a software component of a commissioned node.
Optionally upgrade nodes when a configuration repository changes.
- **Decommission**: Automatically remove configuration, software components, etcd from a commissioned node.
- **Batch operations**: Commission, upgrade or decommission all or a subset number of nodes.
- **Reloads**: In event event of a node reload or unreachability event, verify the node configuration
and automate corrective actions.

###Cluster
The following Cluster management capabilities are provided by Contiv Cluster:

- **Bootstrap**: Remotely bootstrapping a cluster.
- **High Availability**: The cluster manager service is available as long as one node is running
in the cluster.