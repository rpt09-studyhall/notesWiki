**Debugging Together**

Thank you [@MengRS](https://github.com/MengRS) for the awesome guide to debugging together in VS Code!

1.  Ensure host and all guests have live share and live server extensions installed
2.  Host opens workspace in Visual Studio Code
3.  As per usual, host clicks the "Share" button at the bottom left hand corner of the screen and shares the link with the guests:

![](https://github.com/rpt09-studyhall/notesWiki/blob/master/img/image001.png)

4.  Once guests have arrived, the host will click on the "Go Live" button on the bottom right hand corner.

![](https://github.com/rpt09-studyhall/notesWiki/blob/master/img/image002.png)

6.  The host will now need to click on the username and select Share Server:

![](https://github.com/rpt09-studyhall/notesWiki/blob/master/img/image003.png)

7.  The host will type in the exact port number as indicated on the bottom of their screen (e.g. 5500).

![](https://github.com/rpt09-studyhall/notesWiki/blob/master/img/image004.png)

8.  The guest should now click on their username on the bottom left hand corner of their screen and click "Access Shared Server".

![](https://github.com/rpt09-studyhall/notesWiki/blob/master/img/image005.png)

9.  The guest should right click on the shared html file they would like to access and select "Open with Live Server"

![](https://github.com/rpt09-studyhall/notesWiki/blob/master/img/image006.png)

The guest should now be able to open up the file properly on their web browser (see example below).  Note the port number SHOULD / HAS to match up with the host.

![](https://github.com/rpt09-studyhall/notesWiki/blob/master/img/image007.png)

**Troubleshooting:**

* If this doesn't work - it is very possible there is a port sharing issue.  Again, the port the host is sharing HAS to match up with the port the guest is on.  
* Try to do the above steps in order, it's possible if you don't - it will not work.
* If you are still having issues, make sure the host is not sharing multiple port numbers - if they are, have the host delete all the ports that are not in use (host should click on their username and select "Stop Sharing Server") > select the server you want to stop sharing.

![](https://github.com/rpt09-studyhall/notesWiki/blob/master/img/image008.png)
