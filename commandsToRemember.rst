Unix Commands To Remember
****************************

This will be a list of the commands and tricks that I need to remember.



Creating playlists for mplayer
===============================

I use use mplayer from the commandline to play music. It can play from a text file playlist with the ``-playlist`` argument
To generate the playlist I use the find command.

.. code:: bash

    % find "`pwd`" -name "*.mp3" > "playlistname"

The ``"`pwd`"`` gets a full path when the playlist is created

Then call mplayer with the playlist name as an argument

.. code:: bash

    % mplayer -playlist ~/"playlistname"

Also, add the ``-shuffle`` argument to shuffle the playlist.
