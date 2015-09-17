Suspend and Resume on a Thinkpad T420
=====================================


Suspend and Resume seems to work with the following command:

.. code:: bash
    
    % sudo acpiconf -s S3

Networking and USB come up after the resume. The resume happens when the lid is
open and not when the power button is pressed.

You can check your sleep state using:

.. code:: bash

    % sysctl hw.acpi

This command will list the acpi states. I found the following:

.. code:: bash

    hw.acpi.suspend_state: S3
    hw.acpi.standby_state: NONE
    hw.acpi.lid_switch_state: NONE
    hw.acpi.sleep_button_state: S3
    hw.acpi.power_button_state: S5
    hw.acpi.supported_sleep_state: S3 S4 S5


Got most of the info from the `Freebsd <https://www.freebsd.org/doc/en/books/handbook/acpi-overview.html>`_ acpi page. 



Configuring the lid switch
--------------------------

found the code at `freebsd forum <https://forums.freebsd.org/threads/thinkpad-t410i-troubleshooting-resume-from-s3.50713>`_

Running the following will tell you if you have a lid switch.

.. code:: bash

    % dmesg | grep Lid > BSDNotes/scrap/lid.txt


made a change to /etc/sysctl.conf

.. code:: bash

    hw.acpi.lid_switch_state=S3

After reboot the lid switch works. The system sleeps when the lid is closed and
wakes when it is opened.
