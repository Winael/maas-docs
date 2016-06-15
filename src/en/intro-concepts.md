Title: MAAS | Concepts and Terms
TODO: For "Zones", Refer to page equivalent to physical-zones.rst (e.g. fault tolerance)
      Ensure HA link is working


# Concepts and terms

Here are some common terms that are essential to grasp in order to fully enjoy
MAAS, not to mention the rest of this documentation.


## Deploy

To *deploy*, in a MAAS context, is to "utilize". Something that is deployable
is the resource that MAAS ultimately provides that leads to software getting
installed in order to offer up services.

There is an action, in the GUI, called "Deploy" that should not be confused
with the above general notion. This action simply installs an operating system,
typically Ubuntu.

Note that Juju, often used in conjunction with MAAS, also uses the term
"deploy" to mean "deploy a service or an application".


## Nodes

A *node* is a general term that refers to multiple, more specific objects.
Basically, it is a networked object that is known to MAAS.

Nodes include:

- Controllers
- Machines
- Devices


### Controllers

There are two types of controllers: a *region controller* and a *rack
controller*.

A region controller consists of i) the REST API server, ii) the PostgreSQL
database, iii) DNS, iv) proxy (HAProxy), and v) HTTP (GUI). A region controller
can be thought of being responsible for a data centre.

A rack controller provides i) DHCP, ii) TFTP, iii) HTTP (for images), iv)
iSCSI, and v) power management. You need a rack controller attached to each
"fabric". As the name implies, a common setup is to have a rack controller in
each data centre server rack.

Both the region controller and the rack controller can be scaled-out as well
as made highly available. See [MAAS HA](./manage-maas-ha.html) for high
availability.

### Machines

A *machine* is a node that can be deployed by MAAS.

### Devices

A *device* is a non-deployable node. This entity can be used to track
routers, for example.

Devices can be assigned IP addresses (static or dynamic) and DNS names.

They can also be assigned a parent node and will be automatically deleted
(along with all the IP address reservations associated with it) when the
parent node is deleted or released. This is designed to model and manage the
virtual machines or containers running inside a MAAS-deployed node.


## Zones (physical zones)

A *physical zone*, or just *zone*, is an organizational unit that contains
nodes. Zones are most useful when they represent portions of your
infrastructure but they can also be used to simply keep track of where systems
are located. In an HA context, they can be regarded as availability zones
(AZs).

Each node is in one and only one zone and MAAS gets installed with a default
zone in which all nodes get placed.

Zones can assist with fault-tolerance and performance of services running on
the associated machines.


## Regions

A *region* is an organizational unit one level above a zone. It contains all
information of all machines running in any possible zones. In particular, the
PostgreSQL database runs at this level and maintains state for all these
machines.


## Series

A *series* is essentially an operating system version. For Ubuntu, a series
takes into account HWE kernels. In practical terms, a series manifests itself
in the form of install *images* that are used to provision MAAS machines.
Various series are selected by the MAAS administrator.


## Images

An *image* is used to provision a MAAS machine. MAAS images are imported based
on what series have been selected. This is typically done once the install of
MAAS is complete. MAAS only becomes functional once images have been imported.


## Fabrics

A *fabric* is a set of interconnected VLANs that are capable of mutual
communication. A fabric is a logical grouping of unique VLANs. A default fabric
('fabric-0') is created for each detected subnet when MAAS is installed.


## Spaces

A *space* is a logical grouping of subnets that should be able to communicate
with each other. Subnets within each space need not belong to the same fabric.
A default space ('space-0') is created when MAAS is installed and includes all
detected subnets.


## Tags

A *tag* (not to be confused with VLAN tags) is user-created and associated with
nodes based on their physical properties. These can then be used to identify
nodes with particular abilities which can be useful during the deployment of
serivces.