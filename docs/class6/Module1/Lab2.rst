Lab 2:  Configure Device Service Cluster (DSC) High-Availability Settings
-------------------------------------------------------------------------

In Lab 2, we will configure DSC configuration objects, which will assist with establishing a device-trust between BIG-IPs, allowing a successful highly-available Active/Standby BIG-IP pair.

For addtional details on DSC, please refer to this Article: `BIG-IP Device Service Clustering: Administration <https://techdocs.f5.com/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html>`_

Lab Tasks:
==========

* Task 1: Define DSC HA Settings
* Task 2: Configure & Verify Device Trust
* Task 3: Configure the Device Group
* Task 4: Modify Self IP Port Lockdown on Data Self IPs
* Task 5: Add the Management Address to the Failover Network
* Task 6: Create Floating Self IPs
* Task 7: Validate the Device Group Status & Synchronize BIG-IPs

Task 1:  Define Device Service Cluster High-Availability Settings
=================================================================

In Task 1, we will define our respective DSC configuration items on each respective BIG-IP.

Use the following table for the respective configuration objects:

.. note:: Initially, we will ONLY add our Data Interfaces to the Failover Network. This will showcase communication between BIG-IPs.  Management IP will be added in Task X

+-----------------------------------------+---------------------------+-----------------+------------------+
|Device Management Settings:              |Configuration Item / Object|BIG-IP-A IP's    | BIG-IP-B IP's    |
+=========================================+===========================+=================+==================+
|ConfigSync [Local Address]               | HA_vlan_30                |10.1.30.241      | 10.1.30.242      |
+-----------------------------------------+---------------------------+-----------------+------------------+
|Failover Network [Unicast Config]        | Management Address:       |10.1.1.5         | 10.1.1.6         |
|                                         |                           |                 |                  |
|                                         | int_vlan_10:              |10.1.10.241      | 10.1.10.242      |
|                                         |                           |                 |                  |
|                                         | ext_vlan_20:              |10.1.20.241      | 10.1.20.242      |
+-----------------------------------------+---------------------------+-----------------+------------------+
|Mirroring [Primary Local Mirror Address] | HA_vlan_30                |10.1.30.241      | 10.1.30.242      |
+-----------------------------------------+---------------------------+-----------------+------------------+

#. **Navigate to**: Device Management > Devices > click the (Self) hyperlink:

   .. image:: ../images/image18.png

#. If your device name is still the default name *"bipip1,"* click the **Change Device Name** button:

   .. image:: ../images/image153.png

#. Provide the BIG-IP FQDN as the "New Name," change the Certificate drop-down to *Generate New Self-Signed Authority*, and click the **Update** button:


   .. image:: ../images/image154.png


#. Click the "ConfigSync" banner:

   .. image:: ../images/image19.png

#. Under the Local Address drop-down, select the HA VLAN 30 address, then click the **Update** button:

   .. image:: ../images/image20.png

#. Now, we will **ONLY** add our Data Interfaces to our Failover Network configuration, initially.  We will observe the BIG-IP behavior afterwards.  This will showcase failover communication.

#. Click the "Failover Network" banner, then the **Add** button:

   .. image:: ../images/image21.png


#. From the New Failover Unicast Address drop-down, select the data-plane VLAN 10 address and click the **Repeat** button:

   .. image:: ../images/image22.png

#. From the New Failover Unicast Address drop-down, select the data-plane VLAN 20 address; click the **Finished** button:

   .. image:: ../images/image23.png

#. View of the Failover Unicast Configuration, with only the Data Interfaces:

   .. image:: ../images/image157.png

#. Observe the current state of each BIG-IP:
  
   .. image:: ../images/image165.png
   .. image:: ../images/image166.png


+-----------+-----------------------------------------------------------------------------------------------------------------------------------------+
| Question: | Why are both BIG-IPs still **ACTIVE / Standalone**?                                                                                     |
+===========+=========================================================================================================================================+
| Answer:   | Currently, there is no Device Trust between BIG-IPs, so they do not "see" one another. We must establish Device Trust in the next Task. |
+-----------+-----------------------------------------------------------------------------------------------------------------------------------------+

#. Click the "Mirroring" banner:

   .. image:: ../images/image110.png


#. From the Primary Local Mirror Adddress drop-down, select the HA VLAN 30 address, and click the **Update** button:

   .. image:: ../images/image111.png

Upon completion of this Task, both BIG-IPs should remain in an **ACTIVE** and **Standalone** state.  We must establish the Device Trust in the next Task to successfully create our Active/Standby BIG-IP HA pair.

.. note:: 
   - To take advantage of Connection Mirroring, there are addtional BIG-IP configuration items to configure, specifically as it relates to the Virtual Server. We will address this configuration in Lab 3.
   - For information on enabling connection mirroring for your Virtual Server, please refer to this link, `Enable connection mirroring for a virtual server <https://support.f5.com/csp/article/K84303332#s2>`_
   - For more information on Connection Mirroring Configuration, please refer to Knowledge Article `K84303332 <https://support.f5.com/csp/article/K84303332>`_


Task 2: Configure & Verify Device Trust between BIG-IPs
=======================================================

Now we will define the configuration to establish our device-trust between BIG-IPs.

On device *bigipB.f5demo.com*, setup the Device Trust that will be used between BIG-IP systems

NOTE: Observe the current status of EACH BIG-IP. Prior to this Task, they are both in an **ACTIVE / Standalone** state. Throughout this setup, observe the changes in BIG-IP behavior.

.. list-table:: 
   :widths: auto
   :align: center
   :header-rows: 1

   * - State
     - Notes
     - bigipA
     - bigipB
   * - Prior to DSC configuration
     - Both devices in "Standalone" state
     -  .. image:: ../images/image25.png
     -  .. image:: ../images/image26.png
   * - During device peer join trust
     - Both devices enter "Disconnected" state
     -  .. image:: ../images/image27.png
     -  .. image:: ../images/image28.png
   * - After device peer join trust
     - *Both* devices enter "Active / In Sync" state
     -  .. image:: ../images/image29.png
     -  .. image:: ../images/image30.png

#. **Navigate to**: Device Management > Device Trust > Device Trust Members page, then click the **"+"** button to create a new Peer Device:

   .. image:: ../images/image31.png

#. Retrieve Device Credentials (Step 1 of 3):

   Fill in the respective configuration items for *bigipA.f5demo.com*, then click the **Retrieve Device Information** button

   .. image:: ../images/image32.png

#. Verify Device Certificate (Step 2 of 3):

   Confirm the device certificate information, then click the **Device Certificate Matches** button

   .. image:: ../images/image33.png

#. Add Device (Step 3 of 3):

   Verify the device name, and click the **Add Device** button

   .. image:: ../images/image34.png

#. Verify *bigipA.f5demo.com*

   **Navigate to**: Device Management > Device Trust > Device Trust Members

   .. image:: ../images/image35.png

#. Verify that *bigipB.f5demo.com* is shown in the Peer Device List:

   .. image:: ../images/image36.png

+-----------+---------------------------------------------------------+
| Question: | Why are both BIG-IPs Active / In Sync?                  |
+===========+=========================================================+
| Answer:   | There is no Device Group established between the        |
|           | BIG-IPs yet . . . See next task                         |
+-----------+---------------------------------------------------------+

Task 3:  Configure the Device Group
===================================

In Task 3, we will define the device group on the BIG-IPs.

On *bigipA.f5demo.com*, set up the new Device Group that will be used by
both BIG-IP systems.

#. **Navigate to**: Device Management > Device Groups page, and then click the **"+"** button:

   .. image:: ../images/image37.png

#. Create a Device Group using the following information, and then click **Finished** button:

   +-------------+-------------------------------------------------------+
   | Name        | bigip-a_bigip-b_dg                                    |
   +-------------+-------------------------------------------------------+
   | Group Type  | Sync-Failover                                         |
   +-------------+-------------------------------------------------------+
   | Description |  Sync-failover Device Group for BIG-IP A & B          |
   | [OPTIONAL]  |                                                       |
   +-------------+-------------------------------------------------------+
   | Members     | Move both bipipA & bipipB *from the Available* column |
   |             | to the *Includes* column                              |
   +-------------+-------------------------------------------------------+

   .. image:: ../images/image38.png

   .. image:: ../images/image39.png

#.  Observe the current state of each BIG-IP.

   .. image:: ../images/image168.png

   .. image:: ../images/image169.png

+-----------+------------------------------------------------------------------------------------------------------------+
| Question: | Why are both BIG-IPs **ACTIVE** and *Awaiting Initial Sync*?                                               |
+===========+============================================================================================================+
| Answer:   | Both BIG-IPs still cannot "see" their peer due to the current port lockdown settings on the Data Self IPs. |
+-----------+------------------------------------------------------------------------------------------------------------+

#. To confirm each BIG-IP cannot "see" its peer, **Navigate to**: Device Management > Devices, and review the **Status** of the respective BIG-IP peer:
   
   .. image:: ../images/image170.png
   
   .. image:: ../images/image171.png

   
   - In the next Task, we will modify our Self IP port lockdown settings on our Data Self IPs.  This will allow the BIG-IPs to communicate across the Failover IPs.

Task 4: Modify Self IP Port Lockdown on Data Self IPs:
======================================================

In Task 4, we will modify our "Allow None" Self IP port lockdown behavior of the Data Self IPs; we will define a Custom Port Lockdown configuration on the respective Self IPs.

For optimal security, F5 recommends that you use the port lockdown feature to allow only the protocols or services required for a self IP address.

.. note:: For our Data VLANs (internal & external), we will **"Allow Custom"**, allowing **UDP** protocol on port **1026**

There are port lockdown exceptions to be aware of.  Please review Knowledge Article `K17333 <https://support.f5.com/csp/article/K17333>`_ for further details.
 
In Lab 1, when we created our Local Self IPs, we chose to select the "Allow None" port lockdown behavior.  As a result of this, the BIG-IP is preventing DSC communication between BIG-IPs.  In this Task, we will modify our port lockdown configuration, which will allow DSC communication between BIG-IPs.


#. On each BIG-IP, **Navigate to**: Network > Self IPs:

#. Modify both the Internal & External Self IP Port Lockdown settings by clicking their respective hyperlink to modify the item.

   -  Change from "Allow None" to **"Allow Custom"**
      
      - From the Port Lockdown drop-down, select "Allow Custom." 
      - Click the radio button for UDP.  
      - Click the radio button for Port.  
      - In the Port field, enter 1026.  
      - Click Add.
      
      .. image:: ../images/image112.png
      
      You should see "1026" listed in the UDP Custom List section.  Click the **Update** button:
         
      .. image:: ../images/image113.png

   - Repeat this step on the External VLAN

#. Upon completion of this Task, you should observe that the BIG-IPs can start to communicate across on UDP port 1026.  Your BIG-IPs should be in an **ACTIVE/STANDBY** state after this task.

   - BIG-IP-A (is Standby)

   .. image:: ../images/image173.png

   - BIG-IP-B (is Active)

   .. image:: ../images/image172.png

This task validates that your Failover communication must be allowed between BIG-IP

Task 5:  Add the Management Address to the Failover Network
===========================================================

In Task 5, we will add an addtional address to our Failover Network configuration. We will add the Management Address, which will provide an addtional failover path for communication on UDP port 1026.

.. note:: BIG-IP Management Address does not have any default port lockdown settings. If we were to have added this in Task 1, we would have formed a failover communication path on the management IP, allowing the BIG-IPs to communicate. We wanted you to observe how port lockdown settings can affect BIG-IP communication.

#. **Navigate to**: Device Management > Devices > click local BIG-IP hyperlink, then click the Failover Network banner, then click the **Add** button:
   
.. image:: ../images/image174.png

- From the Address drop-down, select the Management Address, and click the **Finished** button:

.. image:: ../images/image162.png

- Upon completion of this Task, you should have three IPs in your Failover Unicast Configuration

.. image:: ../images/image175.png

Task 6:  Create Floating Self IPs
=================================

In this task, we will define Floating Self IP Objects on the **ACTIVE** BIG-IP, which are shared objects between an Active/Standby BIG-IP pair.  

Floating Self IPs are shared objects between BIG-IPs, passing data traffic to the respective **ACTIVE** BIG-IP.  It is a recommended best practice to define a respective floating Self IP object per data segment/VLAN.

For more detailed information regarding Floating Self IPs, please refer to this article:  `Self IP Addresses <https://techdocs.f5.com/en-us/bigip-14-1-0/big-ip-tmos-routing-administration-14-1-0/self-ip-addresses.html>`_

.. note:: Only creating Floating Self IPs on **ACTIVE** BIG-IP. We will then synchronize these settings, proving our DSC communication.

#. Use the following table to create the Floating Self IP Objects:

.. note:: **DO NOT** modify the Floating Self IP Address port lockdown. The Floating Self IP address port lockdown status has to be **Allow None"**

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
     - Traffic Group
   * - [Active]bigip
     - self_vlan10_float
     - 10.1.10.240
     - 255.255.255.0
     - int_vlan_10
     - Allow None (default)
     - traffic-group-1 (floating)
   * - [Active]bigip
     - self_vlan20_float
     - 10.1.20.240
     - 255.255.255.0
     - ext_vlan_20
     - Allow None (default)
     - traffic-group-1 (floating)


#. **Navigate to**: Network > Self IPs, then click the **"+"** button to create a new Self IP:

   .. image:: ../images/image13.png

#. Create the respective Self IPs per the table above.

   VLAN 10 Float:

   .. image:: ../images/image144.png

   VLAN 20 Float:

   .. image:: ../images/image145.png

   After creation of your Floating Self IPs, your Self IP List should reflect the following on BIG-IP-A:
   
   .. image:: ../images/image147.png

Task 7:  Validate the Device Group Status & Synchronize BIG-IPs
===============================================================

In this lab, we have setup BIG-IP Device Trust, and we have created "shared BIG-IP" objects.

In this task, you will observe the current Active/Standby HA state, and synchronize the BIG-IP HA pair.

#. Observe the state of each BIG-IP after Device Group creation

   - bigipA:

     .. image:: ../images/image177.png

   - bigipB:

     .. image:: ../images/image176.png

#. Review the Device Management Overview screen

- **Navigate to**: Device Management > Overview:

  - bigipA:

    .. image:: ../images/image179.png

  - bigipB:

    .. image:: ../images/image178.png

#. Attempt the "Recommendation action", and click the **Sync** button:

   .. image:: ../images/image180.png


+-----------+-------------------------------------------------------------------+
| Question: | Were you able to syncronize the devices?                          |
+===========+===================================================================+
| Answer:   | Yes, we have established successful communication between BIG-IPs |
+-----------+-------------------------------------------------------------------+


#. Validate Devices are In Sync from the Overview page:

   - bigipA:

     .. image:: ../images/image181.png

   - bigipB:

     .. image:: ../images/image180.png



Lab Summary
***********
In this lab, you setup basic BIG-IP Device Service Clustering (DSC) configuration settings.  After completion of these lab tasks, you should have the required configuration to assist in establishing your DSC between BIG-IPs.  Upon completion of this Lab, you should have an **Active/Standby, In Sync** BIG-IP HA pair.

This completes Lab 2.