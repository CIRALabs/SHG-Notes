
* MQTT
** RPC?

* does a flow give us a flow not longer being seen?
* SPIN detection of unexpected flows, and of unexpected volume.
** experimental code.  Anomaly detection. Volume based, new daemon listening
to MQTT. Then uses an API on SPIN to block traffic.
**

* kernel module.
** migrated from kernel to userspace, and now using iptables to manage
traffic.
** iptables: manages traffic, blocking flows.
*** uses iptables directly.
*** openwrt and other technologies, uci configuration files.
***
** another technology for counting data.
**

* available in the API. Indication of a new device.
* configurable as to what the device can do.
* under the hood functionality is the focus.
** handling the profiles on how to do this.

* nodes is a collection of IP addresses that belong to the same destination.
* LIST view of devices and traffic has the API.
** bubble-View is hack.
**


* 2019-03-01: meeting
** had a look at the API, sent some questions to Ad, only could test with 0.7
    version of SPIN.  Questions about associations about NAME and IP
    addresses.  Not sure we can use names directly in the profiles.
    (Email did not get through?.  Found it)

** Ad says: keep the API as stable as possible, uses iptables.

* MCR asks Ad what they are working on.
** make the UCI interface available, automatic compile time option for OpenWRT.
** later on add LUCI to this.
** with one partner if "ubus" would be applicable.  Maybe. Maybe not.
*** supplement MQTT.
*** ubus is a good idea if you have GET/REST scenario, but does not work for publish/subscribe.
** address technical debt, did in a fast way, but have to improve, maybe a memory leak to be removed.
** frequency of updates, it is 1/s, will change it.  Variable?
** identify wifi and limit the access.
** if a new device appears, can get a notification of this... and set a
profile to it (available, "deny all").

* selecting a MUD manager. Jelte is busy with this.  Make that list public.
* want to support several MUD systems.
* TelcoAPI / DOTS Call Home API.  Want to align that with the DOTS
solution. And some additional functionality.  Currently only has Block this
thing, and may need the unblock option, and the inquiry (is it still blocked).

* TelcoAPI might also be useful for port-scanning and other abuse complaints.
* Data export function, so that the contents of SPIN can be sent to other
  destinations. Many security providers want the CPE data to detect if there
  is something wrong with the data.  There is cooperation between Broadband
  Forum and the IETF on this: IPFIX work.
  https://datatracker.ietf.org/doc/draft-boydseda-ipfix-psamp-bulk-data-yang-model/

* Broadband Forum meeting in Poland the week before IETF, and Marco will go
  to the IETF.

* anomaly detection code will appear in tools sections in the coming weeks.

SPIN
----

1. running on Turris.

2. did not have to add package feed... from nic.cz, not 18.06.
   took some convincing to run. default was 192.168.8.1.
   in confluence ... script to change network that it was looking at.

3. depends mqtt/mosquito.  Does not configure mosquito correctly.
   uses mqtt to collect data... web sockets.
   Uses to bubble things from kernel module to webUI.
   No persistence layer.

4. how does SPIN identify compromised systems.
   kernel module does firewall processing.... unsure.

5. what are the rule sets about?  What can we configure?

6. can we filter just for one device behind router?

7. everything is real time, so how to get history of what the device has been
   doing?

8. new device event is sent out.

9.



