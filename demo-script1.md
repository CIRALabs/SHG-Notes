Demonstration Script for Phase 3A.
----------------------------------

Setup beforehand:  verify MAC address of router into Comet Server is marked valid.

1) show router. Plug it into network and power.
2) show console of router

3) bring up log files from Comet Server, and console from Comet Server.
4) show USB key in router, then do FOUR LED reset.

5) observe log file from Comet Server getting provisioning

6) using /root/mudbash on router, and

    tail -f /app/fountain/log/production.log

show log file of router.

7) use "rake shg:dpp_pledge PRODUCTID=xx-yy-zz-aa-bb-cc" to get QR code contents
8) bring up Pledge simulation code in a window, plug laptop/VM/Hermes box
into router with wire.
9) run smarkaklink pledge simulator



