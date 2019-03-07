CIRA Secure Home Gateway Project

Home Gateway Initial Provisioning Design

Problem Statement
* The home gateway is typically new and is flashed with the original firmware from the manufacturer.
* SHG firmware and initial config should be loaded (e.g. flashed)  into the Home Gateway prior to sending the home gateway to the end-users.
* To authenticate later in the provisioning process, each home gateway should have its specific basic key/cert.
* Since we have a goal to have 100 units in alpha testing, the process to flash the SHG firmware and config should be automated as much as possible[a]
* Solutions to the initial provisioning of the SHG firmware may depend on the actual home gateway model and firmware. Typically, the manufacturer firmware has an option to upload a new firmware from its admin config interface. For the Turris Omnia, we will use the USB stick (see 4 LEDs router re-flash). Preferably, the solution should be independent on the method to flash the SHG firmware.
   * The old way of provisioning through Bootp-tftp process does not seem to be available/possible in these environments.
* We should aim towards a solution that might be workable for hackers or partners (i.e. SIDN, Mozilla,....) or even advanced end-users with minimal requirements.


Solution Space

* Given that each home gateway will have its specific key/cert, we need to either build a distinct firmware (with the specific key in it) for each home gateway to flash, or we have a two step process where a generic SHG firmware is loaded and an additional step (that could be in the boot process of the SHG firmware) to further provide the specific key/cert.[b]
* Building a new firmware for each new home gateway is not very complicated, as it could be easily scripted. However, it requires a lot of manual (and careful) manipulations of the files and if USB stick is used, it requires replacing the firmware on the USB stick every time a new home gateway has to be provisioned.
* The two step process, if well implemented, could be much easier to handle and also could provide a way to further update the initial firmware or config. This would be our proposed approach.
* A provisioning server available[c] on the public Internet could be contacted to provision the home gateway in the second step. This server may have some closed policy at the beginning (i.e. only the devices I know will be provisioned) or a more opened policy when we are ready to open it up more (for hackers, partners,...). This way, handling the first 100 devices could be done in a very distributed way with various people provisioning a set of devices.

Proposed Solution

* the SHG firmware contains in the boot script a one-time script that does a http POST query to a server reachable on the Internet.  
* The URL of that server (default is: https://provisioning.securehomegateway.ca/shg-provision) is hardcoded in a file (/etc/shg/provisioningURL) provided in the SHG firmware and read by the script. That enables a user to more easily change the hostname/URL without touching the script.  If the file does not exist, then use the default. (https://provisioning.securehomegateway.ca/shg-provision)
* The contents of the POST’ed file will contain the following information JSON-formatted:
```json
{ “wan-mac” : “0812345678”,
  “switch-mac” : “18419823221”,
  “ula” : “fdba:d505:5c1::/48”,
  “csr” : “base64…”
}
```
* The ULA will be used for management purposes as well as the base to automatically generate a domain label to uniquely address the home gateway by a name.
* The “wan-mac” MAC address is used to uniquely identify the home gateway for the provisioning server. Given that the home gateway have multiple interfaces, the MAC address chosen by the script should be the wan interface with the smallest id, typically “wan” (which is resolved by UCI to the appropriate physical interface).
* The “switch-mac” MAC address is the bridge/switch address of the internal/wifi network and is also sent to the provisioning server so it could be used to generate the QR code for the mobile application.
* The response will contain a .tgz file to be applied on the / of the filesystem. The script will then apply that tgz file and initiate a reboot (a reboot may not really be necessary, but it provides a way for us to even install new capabilities requiring reboot).
* The one-time script is called from the standard boot process, but it looks for the existence of a well-known file on the permanent filesystem. If the file exists, the script just ends. If the file does not exist, then it does the http query and if it receives a successful return, then executes the return and creates the new (empty) file (touch $file).
* The response as a .tgz enables a more generic solution than just returning the key-cert. In the course of the development and in subsequent versions, we may end up having to push more specific files to the home gateway. However, pushing a .tgz which is applied to the / filesystem is dangerous and should be done very carefully.
* Therefore, for the Turris, we would have USB sticks containing the same SHG firmware that would enable fast and reliable config. The turris would need to reach the provisioning server on the Internet, by most likely connecting the outbound wired Ethernet Interface to another home gateway or a network that can reach Internet. The sequence of the first SHG boot will further configure uniquely the home gateway. By monitoring the “database” (tail -f), the person responsible for flashing the home gateways can see which home gateway has successfully booted the SHG firmware and received the specific config.
* That solution also enables static pre-configured keys or configs. The database may be pre-populated by some specific configs for some home gateways.
* The solution also enables us to further reflash the home gateway again (until we ship it to the end user or if it is returned to us) with a new firmware release and any other specific config files, if ever needed.
* The provisioning server have a configured policy (in its config file) with the following states:
   * closed: only provision (i.e respond to POST) home gateways that are already known (i.e. the “wan-mac” address is already in the database).  In this context, a new home gateway user will look at the home gateway box or under the home gateway itself and will read the “wan-mac” address. It will send the “wan-mac” address to the “project support team” that will add that mac address to the white list of the provisioning server.
   * opened: provision any home gateway.
* By using the “wan-mac” address as the unique key for the home gateway, the provisioning server could be configured to even push specific .tgz to specific home gateways. For example, this could be used to test new versions of the firmware on specific home gateways.
* The certs should be letsEncrypt so they are valid certs for any device, browser, app, … However, a mechanism should be put in place to renew them (well, standard certs also need to be renewed, in fact, LetsEncrypt has an automated way to renew). DNS is probably easier than web, but who does it: provisioning server or the SHG?

Provisioning Sequence

1. Turris is in factory mode, powered on, plugged on the Internet on its external interface
2. Plug in the SHG USB stick
   1. contains a modified distro of Turris/Openwrt with SHG firmware
1. Push Reset button and wait until 4 LEDs are on. Release the button.
2. Turris reflash itself using the SHG firmware on the USB stick
3. Remove the USB stick
4. Turris boots on the newly installed SHG firmware
5. The boot script calls and executes the SHG boot script
6. SHG boot script checks the existence of the well-known one-time file /etc/shg/onboarded. If it exists, stop this sequence and continue normal boot sequence. If it does not exist, then continue this sequence.
7. Turris boot has configured its network interfaces and generated a ULA address
8. SHG boot script generates private/public keys, saves them and generates a CSR
9. SHG boot script do an HTTP POST to a well-known URL (the URL of the  provisioning server is hardcoded in a file (/etc/shg/provisioningURL) in the SHG firmware) with the following parameters: MAC-addresses of its wan and switch interfaces, its ULA address and the CSR
10. SHG boot script receives a .tgz file as a response of the HTTP POST with absolute-path files (i.e. starting with /).
11. SHG boot script installs the files on the SHG
12. SHG boot script touches the well-known one-time provisioning file: /etc/shg/onboarded
13. SHG boot script triggers a reboot
14. Turris is rebooted and runs the SHG firmware

TBD

* how to renew letsencrypt certificate given that it will expire. It should be renewed in advance of expiration.
* Viagenie to implement the Turris provisioning client
* polish up this document, for example, by removing any repetition/redundant information, maybe adding a constants section

Possible Future Enhancements

* Investigate EST (RFC703) (a library exists)
