dmenu on lumina
================

I was searching for a way to open programs from the Keyboard in the Lumina Desktop. I had used dmenu in spectrwm and was basically looking for a replacement. 
Searching around the net I found a few snippets on how to get it to run. (found it `here <https://urukrama.wordpress.com/2008/02/07/using-dmenu-in-pekwm-and-openbox/>`_ )

.. code:: bash

	$ dmenu_path | dmenu -b 

This worked from the command line but it wouldn't execute the commands that it found. The -b also puts it at the bottom of the screen. I like it on the top of the screen.  

I then saw down near the bottom of the page that I needed to add some other elements to get it to work. 

.. code:: bash

	$ `dmenu_path | dmenu` && eval "exec $exe"

That worked the way I wanted. Now I just needed a way to get it to launch from a keyboard shortcut.   
I did not see a way to add a keyboard shortcut to the lumina shortcuts gui. It looked like they were predefined. So I looked at doing it in fluxbox itself. the fluxbox config is located in ``~/.fluxbox/keys`` file. I made some changes to that file and restarted fluxbox. 