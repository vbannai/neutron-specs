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

Today in Openstack deployment, there are very limited means to plumb a
VM to a given network. This is typically done by specifying the
networking id or by specfying a pre-created neutron port. In Nova,
there is a concept of host aggregates that provides the capability to
select certain compute nodes with some attributes (key value tags) for
landing VMs. The same capability to select a network based on some
attributes does not exist in neutron.

The networks that are created in neutron (bridged and overlays) do not
have any identifiable tags that help the cloud orchestration software
(eg : nova) to make intelligent decisions. For instance, it is hard
for the cloud orchestration software to differentiate between bridged
and overlay networks. Also, sometimes it would be useful to load
balance the compute load on various switches and routers and create
intelligent and useful topologies. In addition, with the concept of
VPC, it would be nice to indicate the VPC and let the underlying
components make a decision of the appropriate network to pick in the
VPC.  The intent of the network tagging blueprint is to provide a
choice in selecting the subnet to place the VM and also honor any
topological rules and constraints placed by the admin.  The tags would
be represented as a key, value pair and would be stored in a table of
its own. The network resources would then by tagged by the uuid
associated with the key, value pair. The key, value meta information
would be stored in a separate table linked to the network
resource. The approach to storing the meta information would be
similar in concept to provider network extensions.  In addition, as
part of this blueprint, additional blueprints will be submitted to
other Openstack components to support the concept of VPC.


1. Use case (Provider Network Selection)

2. Use Case (Overlay Network Selection)

3. Use Case (VPC) 


Proposed change
===============

The proposal is to enable the core network resources to be tagged by
key, value pairs. These key value pair will be referred to as tags in
this specification. The tags would then be used as filters  based on
hints passed by the entities using the neutron services. 

The proposal is to have a single network tag table that would store
the key value pair along with the unique tag UUID. The proposal also
makes use of an optional database table (tag-resource-mapping) to
associate the tag UUID with the network resources. A given resource
can be associated with mulitple tags. A single tag can also be
associated with multiple resources. 

The implementation is planned along the lines of provider
extensions. The proposal extends the network resources by using
extended attributes to associate tags with the resources.

A typical workflow (for illustrative purposes) for spinning up a VM
might look as follows::
  
  nova boot --flavor=1 --image <image-name> --nic hints={"Mode":
  "Bridge", "key": "value", "key": "value" ..}
  (the made up nova command above is shown for illustrative purposes)

  nova would select the hypervisor based on its filtering rules. After
  the hypervisor is selected, the nova would query the neutron server
  with the hints along with additional hints that nova wants to
  pass to neutron. The hints dicitionary might look like
  hints= nova-hints + {"Mode":"Bridge", "key": "value", "key": "value" }

The reference implementation for this feature will be done using ML2
with Openvswitch driver.


Alternatives 
------------

Alternatives do not exist for these problems. Some solutions have
required the nova compute service to deduce the network id of the VM
either by running a script, looking at configuration data on the
underlying hypervisor.

Data model impact
-----------------

The proposal introduces two optional tables. These tables are not
created if network tagging feature is not used. 

1. Network tag table model.

   The network tag table stores the key value associations
::
    +----------------+--------------+------+-----+---------+
    |     Field      |    Type      | Null | Key | Default |
    +----------------+--------------+------+-----+---------+
    | id             | string(36)   | NO   | PRI |         |
    | name           | string(256)  | NO   |     |         |
    | value          | string(256)  | NO   |     |         |
    +----------------+--------------+------+-----+---------+

2. Network resource tag mapping model.

   The network resource tag mapping model stores the tag id associated
   with the network resource.
::
    +----------------+--------------+------+-----+---------+
    |     Field      |    Type      | Null | Key | Default |
    +----------------+--------------+------+-----+---------+
    | id             | string(36)   | NO   | PRI |         |
    | resource_id    | string(36)   | NO   |     |         |
    | resource_type  | string(256)  | NO   |     |         |
    | tag_id         | string(36)   | NO   |     |         |
    +----------------+--------------+------+-----+---------+


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

Once this feature is in place, additional work needs to be done
through a blueprint to extend passsing of network hints from nova to
neutron during port creation and network selection.  


Testing
=======


Documentation Impact
====================

The documentation has to be upated to show how this feature can be
used along with sample workflows. 

References
==========

* https://blueprints.launchpad.net/neutron/+spec/network-tagging

