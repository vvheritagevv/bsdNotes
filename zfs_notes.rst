ZFS Notes
==========


Recently I had to move some data around and create some datasets for snapshotting. I have a `FreeBSD <http://www.freebsd.org>`_ samba server that runs 4 different shares.
These shares are set up with different permissions for acccess by different users. Ill go into my samba setup in another page. When I initialy set up the server I was very new to the whole ZFS thing. I had heard the greatness but I did not know how to use it yet (btw, I am still new to it but it is never to late to learn). I let the system create the datasets in the way it saw fit. Everything was great. I had mirroring. When one of my disks died, I just used the mirror and bought a new one. Minimal downtime. I was happy. I always backed up offsite, daily. Since we are a tool rental company, our data use on the fileserver is minimal.

All was well until we dealt with our 1st round of the :doc:`cryptolocker </cryptoLocker>` virus.

