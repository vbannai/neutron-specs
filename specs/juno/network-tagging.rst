..
 This work is licensed under a Creative Commons Attribution 3.0 Unported
 License.

 http://creativecommons.org/licenses/by/3.0/legalcode

==========================================================
Neutron Network Tagging - Tagging of the network resources
==========================================================

https://blueprints.launchpad.net/neutron/+spec/network-tagging

Network Tagging implements the ability to label and tag the core
network resources like "network", "subnet" and "ports". The intent of
the network tagging blueprint is to provide a choice in selecting the
subnet to place the VM and also honor any topological rules and
constraints placed by the admin.  The tags would be represented as a
key, value pair and would be stored in a table of its own. The network
resources would then by tagged by the uuid associated with the key,
value pair. The key, value meta information would be stored in a
separate table linked to the network resource. The approach to storing
the meta information would be similar in concept to provider network
extensions.

Problem description
===================

Today in Openstack deployment, there are very limited means to plumb a                               |Neutron Distributed Virtual Router implements the L3 Routers across the
VM to a given network. This is typically done by specifying the                                      |Compute Nodes, so that tenants intra VM communication will occur without
networking id or by specfying a pre-created neutron port. In Nova,                                   |hiting the Network Node. (East-West Routing)
there is a concept of host aggregates that provides the capability to                                |
select certain compute nodes with some attributes (key value tags) for                               |Also Neutron Distributed Virtual Router implements the Floating IP namespace
landing VMs. The same capability to select a network based on some                                   |on every Compute Node where the VMs are located. In this case the VMs with
attributes does not exist in neutron.


Proposed change
===============


Alternatives
------------


Data model impact
-----------------


REST API impact
---------------


Security impact
---------------


Notifications impact
--------------------


Other end user impact
---------------------


Performance Impact
------------------


Other deployer impact
---------------------


Developer impact
----------------


Implementation
==============

Assignee(s)
-----------


Work Items
----------


Dependencies
============


Testing
=======


Documentation Impact
====================


References
==========


