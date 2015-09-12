Moving and Compressing Logs
****************************


I use a simple script with tar and find to move and compress log files from my backups.

.. code-block:: bash

    #!/usr/local/bin/bash

    #Be sure to be in the log file directory

    currDate=$(date +"%m%d%y")
    filePrefix="logbackup_"
    fileExt=".tgz"
    fileName=$filePrefix$currDate$fileExt

    allFiles=$(find . -name '*' -mtime +7)
    tar -czvf $fileName $allFiles

    find . -name '*' -mtime +7 -delete
    mv *.tgz ../logarchive/


There are probably better ways but this works.


I manually run this every Monday. Ill look at a few files 1st and then run the script to compress files over 7 days old. I could automate but this forces me to actually look at some logs.