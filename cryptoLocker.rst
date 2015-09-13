Defeating CyptoLocker With ZFS
*******************************

I recently had the pleasure of fixing not one, but two infections of the Cryptolocker virus. For those of you that are unfamiliar; this is a particularly nasty bug. It makes you thank your stars that you spend so much time worrying about your backups. It encrypts any files on the system that a user would open. Spreadsheets, text documents, even pdfs are not immune. The virus is then nice enough to add a txt, html, and jpg to each directory where it encrypted the files with the ransom info on it. What these criminals want you to do is pay them to unencrypt your own files. The bug will even transverse into any shared drives, thumb drives, or other connected drives. If you have Dropbox or Google drive mapped to a directory, it will get hosed too. Our particular strain came in through an email with a .doc attached. The email was unsolicited but it was stated as being a resume. Since we are always hiring, the two managers that opened the file did not think that there was anything weird about the emails. I have since educated them that we do not open any unsolicited attachments and a quick waterboarding session was ordered...

.. note:: Unless you hate your IT department, never open unsolicited emails or their attachments

The key to defending or mitigating the damage from this is to have good backups, lots of coffee, and keeping sharp objects out of reach. We are a tool rental company, which means that the majority of my users have a hard time even turning on the computer. That is not to say that they are bad people, they are just the bane of technology. EACH. ONE. OF. THEM. We do not use a ton of data, so having backups is relativity easy. One of the 1st things I did when I started here was to setup a `FreeBSD <http://www.freebsd.org/>`_ samba server. I have trained them over the years to save all of their files to the shared directories. I have told them that the only files I will backup are the ones saved to the server. Two users were reminded of that when I told them that they were out of luck for getting the files back from the 2 pc's that initiated the infections. On the server I have ZFS set up with a mirror, Two 500GB disks. I also have a nightly rsync that runs to a backup server that is off site (need one that has ZFS so I can use the tools built into it... looking at you `DigitalOcean <http://digitalocean.com>`_ ).  Before the 1st infection, I was not using the snapshots on a automatic schedule. Boy was that stupid. I got the infection on Thursday. The program had ran for over 2 hours, encrypting anything that it could touch. By the time it was caught, my entire network share was encrypted as well as the pc that initially installed the virus. I immediately took samba down on the server.

.. code-block:: bash

    % sudo service samba stop

Then surveyed the damage. I knew right away that it was a total loss. All of our files had been encrypted. Even though I knew I had backups, I still got a knot in my stomach. I had heard of the snapshot feature in ZFS but I had only used it a hand full of times. I did not have it on a regular schedule. Since the encryption was written to the disk, it also encrypted the files on the mirror. All I had left were the nightly backups. I went about deleting and restoring. By Friday afternoon, all was reset back to the way it needed to be.


Over that weekend I began looking at the snapshot feature more heavily in ZFS. `Michael W. Lucas <https://www.michaelwlucas.com/>`_ had recently given a talk about ZFS and its goodies at the `LivBUG <http://livbug.org/>`_ meeting. We had talked about snapshots and boot environments at the meeting. My desktop at home uses `PcBSD <http://pcbsd.org/>`_ so I was familiar with the snapshots and boot environments from it. I wanted to find something automated like the `PcBSD <http://pcbsd.org/>`_ system. I found a link on Michael's `blog <http://blather.michaelwlucas.com/archives/2140>`_ to `zfstools <https://www.freshports.org/sysutils/zfstools/>`_. By Monday morning I had my snapshots running on schedule.

.. note:: When setting up ZFS, mountpoints, samba shares, and user directories, be sure to make your ZFS datasets correctly. You can never have too many.

When I initially set up this server I did not set it up with snapshots in mind. It was my 1st attempt at ZFS. I used ZFS because `Alan Jude <http://www.allanjude.com/>`_ told me to on the `BSDNow <http://www.bsdnow.tv/>`_ podcast. I did not set up separate datasets for my samba shares. I did not have separate datasets for the individual users home directories. I was using the schedule from Michaels blog post and I was snapshotting everything. Now since my server only uses a total of 13GB, and I have 500GB drives in it, I was not worried about running out of room any time soon. But I had a lot of snapshots that I simply wasnt going to need.

On Monday at 2pm a 2nd manager opened an email with the same cryptolocker virus in it. I did not find out about this one until 7:30 am on tuesday. Once i found out about it I took samba down, and restored my samba drives from the snapshot an hour before the infection.

.. code-block:: bash

    % sudo zfs rollback -r tank/ROOT/default@zfs-auto-snap_hourly-2015-08-31-13h00

While this was effective. It also rolled back some drives that were unaffected by the cryptolocker. I may have been able to roll back specific directories, but I was in a hurry. It took less than 15 seconds for me to be back up and running after the 2nd infection. The 1st infection took me almost 24 hours. That is why you use snapshots ALL THE TIME!!!!

Since then I have separated my data out so that the file shares are in their own
datasets

