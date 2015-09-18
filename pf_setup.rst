Setting Up PF and Fail2ban on the server
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

The skip on lo0 allows for things on the loopback adapter. The *block-policy
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

I then set up for `Fail2ban <http://www.fail2ban.org>`_ 
