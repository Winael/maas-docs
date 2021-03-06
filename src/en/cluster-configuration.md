# Cluster Configuration

Before any of MAAS’s features can be used for the first time, you must
have a cluster controller and configure it to manage at least one
network of nodes. Each node in the cluster should be attached to one of
these networks. (In addition, a node can be attached to any number of
networks that are not managed by MAAS.)

Managing a network normally means that MAAS will serve DHCP from the
cluster controller. **Do this only on a network that was set up with
this in mind.** Running your own DHCP server that competes with an
existing one that’s already managing the network can cause serious
disruption, and it can be hard for administrators to track the source of
the problem. Worse, the problems may not become immediately noticeable.
Make sure you understand the implications of running a DHCP server
before doing this. If MAAS detects any DHCP servers already running on
these networks, it will show them on the cluster’s configuration page.

## Network requirements

The cluster controller manages a network of nodes through one of its
interfaces as defined in MAAS. Cluster interfaces are discovered
automatically, though this may not happen e.g. if the network interface
was down when MAAS was installed.

When a cluster controller manages nodes on a network through one of its
interfaces, the nodes must be on the same subnet as the cluster
interface. This is for two reasons:

1.  If the cluster controller is configured to manage DHCP, the nodes
    must be able to configure their own network interfaces using MAAS’s
    DHCP server. This means that either they must be on the same subnet,
    or that DHCP packets are being specially routed between the nodes’
    subnet and MAAS’s DHCP server.
2.  The cluster controller must be able to find nodes’ IP addresses
    based on their MAC addresses, by inspecting its ARP cache. This
    implies that the nodes and the cluster controller must be on the
    same physical subnet.

## Cluster acceptance

If you install your first cluster controller on the same system as the
region controller, as is the case when you install the full “maas”
ubuntu package, it will be automatically accepted by default (but not
yet configured, see below). Any other cluster controllers you set up
will show up in the user interface as “pending,” until you manually
accept them into the MAAS.

To accept a cluster controller, visit the “pending clusters” section of
the Clusters page:

![](../media/cluster-accept.png)

You can either click on “Accept all” or click on the edit icon to edit
the cluster. After clicking on the edit icon, you will see this page:

![](../media/cluster-edit.png)

Here you can change the cluster’s name as it appears in the UI, its DNS
zone, and its status. Accepting the cluster changes its status from
“pending” to “accepted.”

Now that the cluster controller is accepted, you can configure one or
more of its network interfaces to be managed by MAAS. This will enable
the cluster controller to manage nodes attached to those networks. The
next section explains how to do this and what choices are to be made.

## Cluster interface management


MAAS automatically recognises the network interfaces on each cluster
controller. Some (though not necessarily all) of these will be connected
to networks where you want to manage nodes. A connection between a
cluster controller and a network is called a cluster interface. Each
cluster interface is built on exactly one network interface, though it’s
possible for two cluster interfaces to use the same network interface
card.

We recommend letting your cluster controller act as a DHCP server for
the networks it manages, by configuring the corresponding cluster
interfaces in the MAAS user interface.

As an example, we will configure the cluster controller to manage a
network on interface `eth0`{.docutils .literal}. Click on the edit icon
for the cluster interface on network interface `eth0`{.docutils
.literal}, which takes us to this page:

![](../media/cluster-interface-edit.png)

Here you can select to what extent you want the cluster controller to
manage the network:

1.  DHCP only - this will run a DHCP server on your cluster
2.  DHCP and DNS - this will run a DHCP server on the cluster *and*
    configure the DNS server included with the region controller so that
    it can be used to look up hosts on this network by name.

If you set the interface to be managed, you now need to provide all of
the usual DHCP details in the input fields below. Once done, click “Save
interface”. The cluster controller will now be able to boot nodes on
this network.

There is also an option to leave the network unmanaged. Use this for
networks where you don’t want to manage any nodes. Or, if you do want to
manage nodes but don’t want the cluster controller to serve DHCP, you
may be able to get by without it. This is explained in [*Manual DHCP
configuration*](configure.html#manual-dhcp).

### Static vs Dynamic IP Addresses

On the cluster interface edit page, there are fields to enter both a
dynamic and a static range of IP addresses. It is mandatory to enter the
dynamic range if you are managing DHCP on this interface, but the static
range is optional.

Dynamic addresses are given to both unknown devices booting on this
network, and Nodes that are commissioning. Dynamic addresses are
allocated by the DHCP server and may change at any time.

Static addresses are given to Nodes when they are allocated to a user
and started up, and returned to the pool only when the Node is
de-allocated. Static addresses are allocated by MAAS, and are guaranteed
not to change while allocated. If you are managing DNS on this network,
only static IP addresses are given DNS entries with the Node’s name.

If you do not configure the static range, then nodes will only get
dynamic IP addresses and will never get a DNS entry.

IP addresses in the static range are also available for reservation by
users using the [*MAAS API*](api.html). This prevents MAAS from
allocating the reserved IP to any Nodes or other devices, which allows
users to assign it freely to their own hosts/devices on the same
network, such as LXC containers.

## Multiple networks

A single cluster controller can manage more than one network, each from
a different cluster interface. This may help you scale your cluster to
larger numbers of nodes, or it may be a requirement of your network
architecture.


