Server Maintenance
*******************


To update the os:
=================

.. code-block:: none

    % freebsd-update fetch install

Have to reboot after if there are kernel updates.

I have also set up the root crontab to download updates daily and all I have to do is apply them:

.. code-block:: none

    % vim /etc/crontab

Add the following line:

.. code-block:: none

    @   daily   root    freebsd-update -t "username" cron

Substitute your username for **"username"**

Then just run:

.. code-block:: none

    % freebsd-update install

Updating Optional Software
==========================

Update the ports tree

.. code-block:: none

    % portsnap fetch update

Then update the package sources

.. code-block:: none

    % pkg update

Check to see if what, if anything, needs updating

.. code-block:: none

    % pkg version -vIL=

Look for software vulnerabilities

.. code-block:: none

    % pkg audit -f

Always check */usr/ports/UPDATING* for any info related to your packages

If everything looks good

.. code-block:: none

    % pkg upgrade



Notes and Links
================

This info was mostly gathered from the DigitalOean site. I am putting it here for my own reference with the hope that I can expand in the future as I learn more.

`DigitalOcean FreeBSD Server Maintenance <https://www.digitalocean.com/community/tutorials/an-introduction-to-basic-freebsd-maintenance>`_

