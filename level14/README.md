## Level 14


## Clues


```
level14@SnowCrash:~$ ls -l
total 0
level14@SnowCrash:~$
```

Nothing this time ! 


There is only us, and our old-friend, the getflag command.

```
level14@SnowCrash:~$ which getflag
/bin/getflag
level14@SnowCrash:~$
```


## Solution

We need to do a real reverse engineering, we will use gdb and study the process of getflag.



The getflag commands use the ptrace() function, to check if we are using a debugger.

So we are going to jump to the return of ptrace() and put a postive value.

```
(gdb) br *main+72
Breakpoint 1 at 0x804898e
(gdb) p $eax
$1 = -1
(gdb) set $eax = 1
(gdb) p $eax
$2 = 1
```

We easily guess the getflag use the UID to know which token print.

We are looking for flag14, which is the 3014 UID in the /etc/passwd.

So we jump to the return value of getuid(), which return 2014, the id of our user.

```
(gdb) br *main+444
Breakpoint 3 at 0x8048b02
(gdb) p $eax
$8 = 2014
```

And we edit it to the right one
```
(gdb) set $eax = 3014
(gdb) p $eax
$9 = 3014
```

We end the program
```
(gdb) continue
Continuing.
Check flag.Here is your token : 7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ
[Inferior 1 (process 2071) exited normally]
(gdb)
```


```
level14@SnowCrash:~$ su flag14
Password:
Congratulation. Type getflag to get the key and send it to me the owner of this livecd :)
flag14@SnowCrash:~$ getflag
Check flag.Here is your token : 7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ
```


And this this is the END of the CTF !



It was really fun (this is not true), and i learned a lot of critical issue.

I won't be writing any low-level code the same way, and can now protect myself from injection and a lot of more cyber-things.


Peace !