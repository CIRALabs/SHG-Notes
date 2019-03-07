CIRA Secure Home Gateway Project

Name to IP Address Resolution Considerations and Design


Problem Statement
* Rules from the device’s Manufacturer Usage Description (MUD) are expected to contain hostnames instead of IP addresses. Example: allow outbound traffic to https://provisioningservers.thermostatcompany.com.  
* hostnames can be used in the firewall config of the SHG (expected to be nftables), but doing so means the firewall is doing the name to IP address resolution at the time of reading the rules and installs the IP addresses in the kernel filtering, therefore not updating the IP addresses if the name to IP address resolution changes. Written differently, the lifetime of the installed resolved rule is much longer than the typical lifetime of a DNS name.
* Manufacturers would likely to use cloud services, which may involve “tricks” in DNS resolution, such as TTL value of zero for load balancing, different resolved IP addresses based on topology location, etc… These addresses may change “often” as cloud services are often pretty dynamic.
* Therefore, the installed rules may become obsolete and the device may not function properly anymore. For example, an alarm system or thermostat that can be remotely controlled would not be able to reach its servers, therefore essentially not working from the perspective of its connected use case.


Components
* the MUD controller reads the MUD files and installs the rules in the OS firewall


Possible Solutions
1. DNSCAP has been identified as a possible solution. DNSCAP is a software that can capture DNS traffic, enabling its analysis. Capturing the DNS traffic will contain a lot of noise (all kind of requests coming from all devices, guest iphones, .. from the internal network), therefore will need to be carefully filtered. Knowing the records and their associated TTL does not solve that much of the problem.
2. for each hostname found in the activated rules, the MUD controller does the DNS query for the mapping of names and IP addresses and store the results into a watch table in memory/sqlite/... A watcher is set to do periodic name resolution. When the watcher discovers a change in the mapping, it can trigger to reset/reread the rules so that the firewall will redo its name to IP address resolution. The rules are therefore indirectly updated. The watcher can be smart by keeping track of record TTLs or can be dumb by being spawned at some specific frequency (ex: every 10 min). The watcher could be a thread in the MUD controller or a separate process which communicates with the MUD controller by some means (API/inter-process/db/file/signal/...)

Recommendation
* We recommend to do B as a thread inside the MUD controller, as it seems to be the best reliable solution.
