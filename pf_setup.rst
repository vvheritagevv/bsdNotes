Setting up PF and Fail2ban on the server
=========================================


Added to **/etc/rc.conf**

.. code:: bash

    pf_enable="YES"
    pf_rules="/etc/pf.conf"
    pflog_enable="YES"
    pflog_logfile="/var/log/pflog"


A few things to keep in mind with firewalls; It is very easy to lock yourself
out of it. Until you are very comfortable with it, have the console handy.
You may have to use it. Make 1 or 2 changes and test. Build the rule set slowly
that way you will know what locks you out of the system if it does. There are
many features to `PF <http://www.openbsd.org/>`_ and I wont go over them all.
Ill show what I use here.

1st I set up some defaults:

.. code:: bash

    set skip on lo0
    set block-policy return
    scrub in all
    antispoof quick for lo0 inet
    block in from no-route to any

The *skip on lo0* allows for things on the loopback adapter. The *block-policy
return* will have send the *Connection refused* message back to the sender.
*scrub in all*  normalizes fragmented packets. *antispoof quick for lo0 inet*
will block packets which come from interfaces that are not possible. Finally we
block anything that we have no route back to.

Next I block everything and log it if it does not match any rule below.

.. code:: bash

    block in log

.. note:: Remember that in pf last rule match wins unless the *quick* parameter is added

Then I allow out all traffic

.. code:: bash

    pass out keep state

I then set up for `Fail2ban <http://www.fail2ban.org>`_. In the **/usr/local/etc/fail2ban** edit the **jail.d/ssh-pf.local** file.
Use the following settings:

.. code:: bash
    [ssh-pf]
    enabled = true
    filter = sshd
    action = pf
    logpath = /var/log/auth.log #location of the logfile that you want fail2ban to parse
    findtime = 600
    maxretry = 3
    bantime = 3600

Then in the **action.d/pf.conf** make sure that the following is in it:

.. code:: bash

    [Definition]
    actionstart =
    actionstop =
    actioncheck =
    actionban = /sbin/pfctl -t <tablename> -T add <ip>/32
    actionunban = /sbin/pfctl -t <tablename> -T delete <ip>/32

    [Init]
    tablename = fail2ban

