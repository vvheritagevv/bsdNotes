Git Server
=============


I decided to set up a private git server for working on things that may be a bit to unpolished or
sensitive to put on git hub. I also added a dataset in ZFS so that it would be snapshotted separate from root. Followed a few guides and this is what I did in the end.



All examples in this are pulled http://bin63.com/how-to-setup-a-git-repository-on-freebsd and the Freebsd handbook ZFS chapter.


Add the Groups:

.. code:: bash

	# pw groupadd -n git -g 9418
	# pw useradd -n git -u 9418 -g git -c git -d /git -s /usr/local/libexec/git-core/git-shell -h -
	# mkdir /git/

Since I am not using ssh keys in this example I have to set a password for the git user:

.. code::bash

	# passwd git

Then set up the permissions:

.. code:: bash

	# chown git:git /git/
	# chmod 755 /git
	# mkdir /git/base/
	# chown git:git /git/base/
	# chmod 775 /git/base  

At this step the guides tell you to setup ssh keys. I will add that later but did not do it at this time. 

I then modified /etc/rc.conf for git:

.. code:: bash

	git_daemon_enable="YES"
	git_daemon_directory="/git"
	git_daemon_flags="--syslog --base-path=/git --export-all --reuseaddr --detach"

Then start git_daemon:

.. code:: bash

	/usr/local/etc/rc.d/git_daemon start

At this point I created the separate dataset for the /git and /git/base directories. With zfs you have to create the parent and the child dataset though you only set a mount point for the one that you want to use. There is probably a way to do this with one command but I always create them separate.
I also set the mount point in a separate command, again there is probably a way to do this with one command but this works so I don't worry about it. 

.. code:: bash

	# zfs create -o compress=lz4 tank/git
	# zfs create -o compress=lz4 tank/git/base
	# zfs set mountpoint=/git/base tank/git/base

Now it is time to test and use the git server. You have to set up the repo on the server before you can push to it from the client. 

.. code:: bash

	% mkdir /git/base/test.git
	% cd /git/base/test.git && git init --bare --shared

That should be good enough but for reasons unknown, I was unable to push to the repo until I changed the ownership.

.. code:: bash

	# chown -R git:git .

On the client machine create a new repo and add the origin to point to the server repo that we just created. 

.. code:: bash

	% mkdir test
	% cd test && git init
	% touch foo
	% vim foo ## add some text
	% git add foo
	% git commit -m 'test commit'
	% git remote add origin git@the.hostnameUhave.com:base/test.git
	% git push origin master

On thing that tripped me up is that on the server you wont find the actual files that you are pushing. Not sure how it actually works but I pushed some files, deleted the local repo, and then cloned the repo and my files are back. When looking for the files on the server they are no where to be found in the dir tree.  