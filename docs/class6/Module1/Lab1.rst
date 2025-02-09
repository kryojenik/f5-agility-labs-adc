Lab 1: Configure BIG-IP Trunks, VLAN's, and Self-IP's
-----------------------------------------------------

In Lab 1, we will setup basic network-level settings on our BIG-IPs.  We will define Trunks, VLANs, and local Self IPs.  These configuration items will assist in establishing connectivity to/from our BIG-IPs, and between BIG-IPs.

Lab Tasks:
==========

* Task 1: Create BIG-IP Trunks
* Task 2: Create BIG-IP VLANs
* Task 3: Create BIG-IP Self IPs

Task 1: Create BIG-IP Trunks
============================

The BIG-IP platform consists of the underlying CENTOS linux that is mainly used to boot TMOS (Traffic Managment Operating System). 
TMOS is the high performance proxy dataplane of BIG-IP. It handles all packets going into and leaving BIG-IP.

A big challenge is that BIG-IP TMOS has no exposure to the physical link state. It does not know if a link is up or down. 

In the past this limitation was handled by a feature called "VLAN Failsafe". 
VLAN Failsafe monitored the traffic on a specific VLAN and acted if no traffic was received. This method took between 10 and 40 seconds to detect a physical link failure.

A better way to failover on Layer 2 link failure is the use of Trunks and High availability (HA) groups.

A trunk can be used for Link Aggregation or channeling of multiple physical interfaces in one bigger pipe.
At the same time a trunk can have only a single interface applied. 

BIG-IP TMOS can see the number of interfaces in a trunk. So we will use this ability to track the link status if a physical interface is up or down. 


In Task 1, we will define BIG-IP trunks.  These trunks will be used in subsequent labs, becoming part of our HA Group configuration.

#. On both BIG-IP devices, configure trunks under the Network configuration section.

   Use the following table to create & define your three Trunks:

   +----------------+----------------------+-------------------------+
   | **Trunk Name** | **Interface Member** | **Description /         |
   |                |                      | Function**              |
   +================+======================+=========================+
   | int_trunk      | 1.1                  | Trunk to simulate a     |
   |                |                      | connection to internal  |
   |                |                      | infrastructure          |
   +----------------+----------------------+-------------------------+
   | ext_trunk      | 1.2                  | Trunk to simulate a     |
   |                |                      | connection to external  |
   |                |                      | infrastructure          |
   +----------------+----------------------+-------------------------+
   | HA_trunk       | 1.3                  | Trunk to simulate a     |
   |                |                      | high-availability       |
   |                |                      | network connection      |
   +----------------+----------------------+-------------------------+

#. **Navigate to**: Network > Trunks > Trunk List, then click the "+" button to create a new Trunk:

   .. image:: ../images/image1.png

#. Provide a Trunk Name, and move the respective Available interface to the "Members" section.

#. Click Repeat to define your next trunk.

   When you define the last trunk, you may select the "Finished" button

   - Internal Trunk:
   
     .. image:: ../images/image2.png


     .. image:: ../images/image3.png

   - External Trunk:

     .. image:: ../images/image4.png

   - HA Trunk:

     .. image:: ../images/image5.png

   - View of Trunk List after creating all three trunks:

     .. image:: ../images/image6.png


Task 2: Create BIG-IP VLANs
===========================

In Task 2, we will define our VLANs on our BIG-IPs.  Our VLANs will be associated with their respective trunk from Task 1.

On both BIG-IP devices, configure VLANs under the Network configuration section.

Use the following table to create & define your three VLANs:

+------------+----+-----------+----------+
|Name        |Tag |Interface  | Tagging  |
+============+====+===========+==========+
|int_vlan_10 | 10 |int_trunk  | Untagged |
+------------+----+-----------+----------+
|ext_vlan_20 | 20 |ext_trunk  | Untagged |
+------------+----+-----------+----------+
|HA_vlan_30  | 30 |HA_trunk   | Untagged |
+------------+----+-----------+----------+

#. **Navigate to**: Network > VLANs > VLAN List, then click the "+" button to create a new VLAN:

   .. image:: ../images/image7.png

#. Create the respective VLANs per the table above.

   - Internal VLAN:

     .. image:: ../images/image8.png

     .. image:: ../images/image9.png

   - External VLAN:

     .. image:: ../images/image10.png

   - HA VLAN:

     .. image:: ../images/image11.png

   - View of the VLAN List after all VLANs have been defined, and associated to their respective Trunk:

     .. image:: ../images/image12.png

Task 3: Create BIG-IP Self IPs
==============================

In Task 3, we will configure our Local Self IPs of each BIG-IP.  These IPs will be our L3 connectivity to our BIG-IP networks.

On both BIG-IP devices, configure their respective Self IPs under the Network configuration section.

Use the following table to create & define your three Self IPs:

.. list-table:: 
   :widths: auto
   :align: center
   :header-rows: 1

   * - BIG-IP
     - Name
     - IP address
     - Netmask
     - VLAN
     - Port Lockdown
   * - bigipA
     - self_vlan10
     - 10.1.10.241
     - 255.255.255.0
     - int_vlan_10
     - Allow None (default)
   * - bigipA
     - self_vlan20
     - 10.1.20.241
     - 255.255.255.0
     - ext_vlan_20
     - Allow None (default)
   * - bigipA
     - self_vlan30
     - 10.1.30.241
     - 255.255.255.0
     - HA_vlan_30
     - Allow None (default)
   * - bigipB
     - self_vlan10
     - 10.1.10.242
     - 255.255.255.0
     - int_vlan_10
     - Allow None (default)
   * - bigipB
     - self_vlan20
     - 10.1.20.242
     - 255.255.255.0
     - ext_vlan_20
     - Allow None (default)
   * - bigipB
     - self_vlan30
     - 10.1.30.242
     - 255.255.255.0
     - HA_vlan_30
     - Allow None (default)

#. **Navigate to**: Network > Self IPs, then click the "+" button to create a new Self IP:

   .. image:: ../images/image13.png

#. Create the respective Self IPs per the table above.

   - Self IP, VLAN 10:

     .. image:: ../images/image14.png

   - Self IP, VLAN 20:

     .. image:: ../images/image15.png

   - Self IP, HA VLAN 30:

     .. image:: ../images/image16.png

   - Example view of the Self IP List from BIG-IP-A after all Self IPs have been defined:

     .. image:: ../images/image17.png


Lab Summary
===========
In this lab, you setup basic BIG-IP network-level configuration settings.  After completion of these lab tasks, you should have network connectivity between the devices on all VLANs.  These configuration objects will assist with the subsequent labs.

Observe the current state of each BIG-IP.  At this time, both BIG-IPs should be in an **ACTIVE** and **Standalone** state.  In the following labs, we will establish a successfull highly-available Active/Standby BIG-IP pair.

This completes Lab 1.