OpenVSwitch includes:
====================

* OVN
* OVN-NB -- connects OpenStack (e.g) to OVN_Southbound datbase.
* 


OVN Architecture
----------------
    * http://www.openvswitch.org/support/dist-docs-2.5/ovn-architecture.7.html
    * http://www.openvswitch.org/support/dist-docs-2.5/ovn-controller.8.html

OVN is overkill for our purposes

OVS kernel
----------
* uses kernel bridge hooks (incompatible with Bridge)
* ovsdb-server manages a private database (probably sqlite3)
* building with DPDK support requires 1GB kernel support, "experimental"

Why
---
http://www.openvswitch.org/support/dist-docs-2.5/WHY-OVS.md.html
    * The mobility of state
    * Responding to network dynamics
    * Maintenance of logical tags
    * Hardware integration



Further useful links
---------------------
    * http://www.openvswitch.org/support/dist-docs-2.5/INSTALL.md.html
    * ovs-sandbox - in openvswitch-test package


```
                                         CMS
                                          |
                                          |
                              +-----------|-----------+
                              |           |           |
                              |     OVN/CMS Plugin    |
                              |           |           |
                              |           |           |
                              |   OVN Northbound DB   |
                              |           |           |
                              |           |           |
                              |       ovn-northd      |
                              |           |           |
                              +-----------|-----------+
                                          |
                                          |
                                +-------------------+
                                | OVN Southbound DB |
                                +-------------------+
                                          |
                                          |
                       +------------------+------------------+
                       |                  |                  |
         HV 1          |                  |    HV n          |
       +---------------|---------------+  .  +---------------|---------------+
       |               |               |  .  |               |               |
       |        ovn-controller         |  .  |        ovn-controller         |
       |         |          |          |  .  |         |          |          |
       |         |          |          |     |         |          |          |
       |  ovs-vswitchd   ovsdb-server  |     |  ovs-vswitchd   ovsdb-server  |
       |                               |     |                               |
       +-------------------------------+     +-------------------------------+
       ```


OVS research
------------
* https://www.researchgate.net/publication/306048090_MAC_Based_Dynamic_VLAN_Tagging_with_OpenFlow_for_WLAN_Access_Networks
* http://docs.ruckuswireless.com/fastiron/08.0.80/fastiron-08080-sdnguide/GUID-6159BAD5-D4E8-4A84-9969-CD5A0E42A985.html
* https://github.com/mininet/openflow-tutorial/wiki/Create-a-Learning-Switch

EBtables info
-------------
* http://ebtables.netfilter.org/br_fw_ia/br_fw_ia.html
* https://upload.wikimedia.org/wikipedia/commons/3/37/Netfilter-packet-flow.svg



