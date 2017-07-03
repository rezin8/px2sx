#Using the PX2SX.pl script.
====================================================

##Installation:

1. Download and install Strawberry Perl from http://strawberryperl.com/ (will requre Run Elevated)
2. Download the px2sx.pl repository from github: https://github.com/rezin8/px2sx. Extract it to the C:/Strawberry directory.
3. From the command prompt, move to C:\Strawberry.
    1. px2sx.pl requires the NetAddr::IP dependency. In order to install that, we can use cpan.
4. From C:\Strawberry, enter: cpan -i NetAddr::IP
    1. This may take a while. cpan is going to reach out to download the needed files, then install them. It may fail. If it does, wait a bit and try again.
	
--------------------------------------------------------------------------------
	
##Usage:

Now you should have everything you need installed to use the script. Here's the command info.

C:\Strawberry>perl px2sx.pl

Usage:  perl px2sx.pl [-cgjns] [-p <STRING>] [-z <zone.file>] <config.file>

Flags:	-c Create .CSV files as well as Junos configuration  
        -g Write address objects to the global address-book  
        -j Generate config file syntax instead of set commands  
        -n Do not combine ACLs, ie: one policy per ACL  
        -s Partial ACL combining; services only  
        -p <STRING> Prepend security policies with this string  
        -z <zone.file > Do not create a Zone file, use this one instead  

Before we use the command, there's some housekeeping that we need to take care of. Notepad++ will make this easier. It can be downloaded from https://notepad-plus-plus.org/. Obviously you can also use your favorite text editor, but I'll only document the commands for Notepad++.

1. Create a new file named "zones.txt" in the C:\Strawberry directory. In that file, we'll need 2 things:  
    1. RFC 1918 Address Space for B2B-INSIDE  
    2. Client assigned address space  
        1. Your file will look like this, with the client info corrected, of course:  
            B2B-INSIDE,10.0.0.0/8  
            B2B-INSIDE,172.16.0.0/12  
            B2B-INSIDE,192.168.0.0/16  
            *CLIENT-ZONE-NAME*,10.XXX.0.0/16  
2. Open the ASA config file. We'll need to remove any remarked lines in the ACL. 
    1. Ctrl+H to bring up the Replace menu (or Search -> Replace)
    2. Click "Regular Expression" in the bottom left Search Mode box.
    3. In "Find What", enter: .*remark.*
    4. Leave "Replace With" blank.
    5. Click "Replace All". Close the Find box.
    6. Click the Edit menu -> Line Operations -> Remove Empty Lines (Containing Blank characters). This will remove all the blank lines created by the Replace command.
    7. Save the new, edited ASA config to the C:\Strawberry directory as a .txt file, not a .cfg or anything else. ONLY .TXT
3. Now it's time to run the command and do the conversion. 
    1. From C:\Strawberry, run: perl px2sx.pl -gn -z zones.txt *NEW_ASA_CONFIG*.txt
    2. The script will run and output numerous files, and will probably kick back some errors. You can usually ignore the errors, but check them just to be sure.
        1. The files added will be:  
            adds.txt - This is the address book for individual hosts.  
            add-sets.txt - The address book for address-sets.  
            apps.txt - The individual applications  
            app-sets.txt - The application-sets.  
            pols.txt - This is the security policy.  
            nats.txt - Uhh.... NATS!  
            routes.txt - Ignore this... not our problem.  
4. Now the heavy lifting of the conversion has been done, but there's still going to be issues to clear up. You'll need to go through the new config and make sure that everything is correct. You'll probably find that the script 
	
