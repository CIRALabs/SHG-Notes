How to install SecureHomeGateway on new hardware
------------------------------------------------

1. Flash SHG medkit:

Medkit image is available at: https://www.viagenie.ca/cira-shg/medkit/omnia-medkit.tar.gz
Copy the tar.gz on a USB drive, and follow https://doc.turris.cz/doc/en/howto/omnia_factory_reset#re-flash_router

2. Boot system, preferably with a serial console.
The WAN interface will be the uplink, plug it in.

There is no foris/luci/root password set by default. You can configure it by going to http://192.168.1.1/foris/config/main/password/

3. Reboot

