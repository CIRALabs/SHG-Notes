Based upon http://www.gnuton.org/blog/2016/02/lxc-on-openwrt/

{{{
    opkg update
        opkg install iptables-mod-extra kmod-ipt-extra lxc-start lxc-execute \
        lxc-info \
        getopt xz lxc lxc-attach lxc-autostart lxc-cgroup lxc-checkconfig \
        lxc-common lxc-config lxc-configs lxc-console lxc-create lxc-destroy \
        lxc-execute lxc-freeze lxc-hooks lxc-info lxc-init lxc-ls lxc-lua \
        lxc-monitor lxc-monitord lxc-snapshot lxc-start lxc-stop lxc-templates \
        lxc-unfreeze lxc-unshare lxc-user-nic lxc-usernsexec lxc-wait \
        lxc-unshare bash debootstrap
}}}

The lxc-clone package seems to have disappeared.

You need an RSYNC that has ACL support, this requires entering
Network/FileTransfer, Rsync and then enabling ACLs.

You also need a full iproute2 package, which is in Networking/Routing and Redirection/ip-full.

Then,
{{{
opkg install rsync kmod-veth
}}}





        

