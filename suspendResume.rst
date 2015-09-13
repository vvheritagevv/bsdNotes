Suspend and Resume on a Thinkpad T420
=====================================


Suspend and Resume seems to work with the following command:

.. code:: bash
    
    % sudo acpiconf -s S3

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

TODO find the lid switch and configure it
