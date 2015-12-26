OpenBSD Wireless Bridge
************************

Sometimes it is just easier to do things yourself. I had issues with my internet access at my Novi
location on 12/23. Put the ticket in with the ISP at 3pm and long story short, its 10:52pm and I still have
not talked to a Tech..... I needed to batch out my Credit card machine. It connects over the internet to a
server at the credit card processing service. I had my Windows 7 laptop as well as my OpenBSD laptop with me.
To be clear, I LOVE FreeBSD and to a lesser extent OpenBSD, but for $reasons I run Windows on my work
laptop. I am not a Windows hater by any stretch, it has its place, but I do prefer the BSD's. I had to come up
with a way to get my ethernet only credit card machine on the net to process the batch. I knew I could use my
cell phone to create a wifi hotspot and then share that connection through Windows. I was able to do that but
I could not figure out how to set the lan network to the 192.168.30.0/24 block that the credit card machine is
in. I did not want to mess with the settings on the credit card machine. I fooled around with it until I grew
too frustrated. It should be simple. I then remembered that my OpenBSD laptop was in the car too. I thought
I'll never have enough time to figure this out before the Tech gets here but I decided I would try to figure
it out. I did get it to work and transmit my batch. I will outline my steps below.

1st Things 1st, Connect to the hotspot
=======================================

I had never connected the OpenBSD laptop to my phone hotspot. I had to figure that out 1st.
In the /etc/hostname.run0 file I have settings for my wireless at home. When on the road I put the Wifi values
in with the ifconfig command. it took some trial and error but i got it to work with the following

.. code:: bash

    $ doas ifconfig run0 nwid "The Phone WIFI" wpa wpaprotos wpa2 wpakey "thewpakey"
    $ doas ifconfig run0 up
    $ doas dhclient run0

Not sure if I needed the wpaprotos part as I think it defaults to wpa2. I got all of this from the man pages
for ifconfig.


Now the real fun begins
=========================

The next step was to set up the network for Nat and set the ipaddresses. I ended up using the
*/etc/hostname.nfe0* file to set things up. I am sure that I could have done the same thing with an
ifconfig command as that wouldn't have made it permanent and I wouldn't have needed a reboot but we live
and learn. Here is the file:

.. code:: bash

    inet 192.168.30.1 255.255.255.0 192.168.30.255
    description "Lan for sharing WiFi"

From the guides I was reading I should have been able to run */etc/netstart* but even when ran as root I got a
permission denied error. So I did what any self respecting Windows user would do, I rebooted.
I then added the following to the */etc/sysctl.conf* file to allow forwarding

.. code:: bash

    net.inet.ip.forwarding=1
    net.inet.ip.redirect=0

This will make them persist past a reboot but I needed them for the current session. I wasn't about to Windows
this thing again (you know, reboot it) so I used sysctl

.. code:: bash

    $ doas sysctl net.inet.ip.forwarding=1
    $ doas sysctl net.inet.ip.redirect=0


Tie it all together
====================

The next step was to get nat setup with the PF firewall. The config file is */etc/pc.conf*. I made a backup
and began my edits.

.. note:: This is probably highly insecure but it was built to quickly serve one purpose. I will update when I nail it down more.

I initially deleted all the lines in the file and added the following:

.. code:: bash

    set skip on lo

    if_wan = "run0"
    if_lan = "nfe0"

    pass out on $if_wan from $if_lan:network to any nat-to $if_wan

    pass in quick on $if_wan from any to any

    pass out on $if_lan

Again, this maybe completely insecure and offbase but it worked for the quick purpose that it served. I reloaded the firewall

.. code:: bash

    $ doas pfctl -f /etc/pf.conf

I connected the lan of the laptop to a switch. Plugged the Windows laptop in one of the ports on the switch. I
set the ethernet address to one in the 192.168.30.0/24 block set DNS to the Google servers 8.8.8.8 and
8.8.4.4. I was then able to ping Google and surf the web from the Windows laptop through the OpenBSD wireless
connection.
I then connected the credit card machine and ran the batch.

Conclusion
===========

As of this writing I still do not have a tech at my location. It is 11:54pm. So I can learn how to build a
router and blog about it before my ISP can address the issues. This was surprisingly easy to accomplish. I
will extend this document as I work on this laptop. I want to be able to use this if I have a location down in the future. Maybe VPN in and have all the shares work and have DHCP on the lan side.
