dmenu on lumina
================

I was searching for a way to open programs from the keyboard in the Lumina Desktop. I had used dmenu in spectrwm and was basically looking for a replacement. 
Searching around the net I found a few snippets on how to get it to run. (found it `here <https://urukrama.wordpress.com/2008/02/07/using-dmenu-in-pekwm-and-openbox/>`_ )

.. code:: bash

	$ dmenu_path | dmenu -b 

This worked from the command line but it wouldn't execute the commands that it found. The -b also puts it at the bottom of the screen. I like it on the top of the screen.  

I then saw down near the bottom of the page that I needed to add some other elements to get it to work. 

.. code:: bash

	$ `dmenu_path | dmenu` && eval "exec $exe"

That worked the way I wanted. Now I just needed a way to get it to launch from a keyboard shortcut.   
I did not see a way to add a keyboard shortcut to the lumina shortcuts gui. It looked like they were predefined. So I looked at doing it in fluxbox itself. The fluxbox config is located in ``~/.fluxbox/keys`` file. I made some changes to that file and restarted fluxbox. and that did not work. I could not get any of the .fluxbox keys to work. 
I did more web searching and found that lumina gets the keyboard shortcuts from a config in ``~/.lumina/lumina-keys`` file. I could change them in there. 

.. code:: bash
	
	Mod1 P :Exec `dmenu_path | dmenu ` && "exec $exe"

I added that line to the config. That maps alt-p to open dmenu. One thing that I noticed, if you change a map this way, it does not activate right away. I had alt-p mapped to lumina-search. When i changed it to the above line, it did not update it to dmenu. Logging out and back in again solved the issue. There may be a command to run to automatically update, probably look at the source for the gui app as it has an apply feature that seems to do it, but I was able to get it to work regardless. 

.. note:: As with anything you find on the web, I could be completely and utterly wrong. Do your own due diligence and be careful when you copy/paste shit from the web. If I have something wrong, and you want to help correct it, shoot me an email. 

