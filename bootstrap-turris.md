How to install SecureHomeGateway on new hardware
------------------------------------------------

1. Flash Turris OS4.0.
    See: https://forum.turris.cz/t/turris-os-4-0-here-be-dragons/8384

A copy of the relevant file is at:
    http://shg.sandelman.ca/firmware/omnia-medkit-4.0.tar.gz

    ada93787e52948ca3b4dd1c2291e7e850811662ed747af7cdd646cf2d17c5017  omnia-medkit-4.0.tar.gz

2. Boot system, preferably with a serial console.
   If no serial console, then go through normal Turris setup, set up the
   LUA root password, and then enable ssh, and login as root.

3. Some parts of the system are still based upon an rsync of an LXD container.
This requires an ssh key to be populated to shg@shg.sandelman.ca in order to
perform the rsync.

    opkg update
    opkg install openssh-client
    opkg install rsync

    ssh-keygen

4. Send the /root/.ssh/id_rsa.pub key to mcr@sandelman.ca.

5. Now start an rsync of the container contents:

    host=shg.sandelman.ca
    rsync --rsync-path="rsync --fake-super" -x --delete -a -r --inplace --exclude=SWAP --exclude=/etc --partial shg@$host:/shg/builds/shgturris/srv/. /srv/.

6. Setup Custom Feeds to get new packages:

   root@hera:~# cat /etc/opkg/customfeeds.conf
   # add your custom package feeds here
   #
   # src/gz example_feed_name http://www.example.com/path/to/files
   src/gz cira_packages    http://herring.sandelman.ca/packages/arm_cortex-a9_vfpv3/ciralabs
   src/gz herring_base     http://herring.sandelman.ca/packages/arm_cortex-a9_vfpv3/base
   src/gz herring_package  http://herring.sandelman.ca/packages/arm_cortex-a9_vfpv3/packages
   src/gz herring_luci     http://herring.sandelman.ca/packages/arm_cortex-a9_vfpv3/luci

   # echo "untrusted comment: Local build key
   RWSpaheDt1Pk2CRfMizPsXk5wgHHVjgpCeVFShl3bE8365zB4FC3J915" >/etc/opkg/keys/a96a1783b753e4d8

   root@hera:~# opkg update
   Downloading http://herring.sandelman.ca/packages/arm_cortex-a9_vfpv3/ciralabs/Packages.gz
   ...

7. Install our components:

   opkg install mud-supervisor

8. Reboot




