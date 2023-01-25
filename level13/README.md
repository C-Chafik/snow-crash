## Level 13

## Clues

```
level13@SnowCrash:~$ ls -l
total 8
-rwsr-sr-x 1 flag13 level13 7303 Aug 30  2015 level13
level13@SnowCrash:~$
```


```
level13@SnowCrash:~$ ./level13
UID 2013 started us but we we expect 4242
level13@SnowCrash:~$ ltrace ./level13
__libc_start_main(0x804858c, 1, 0xbffff804, 0x80485f0, 0x8048660 <unfinished ...>
getuid()                                                                  = 2013
getuid()                                                                  = 2013
printf("UID %d started us but we we expe"..., 2013UID 2013 started us but we we expect 4242
)                       = 42
exit(1 <unfinished ...>
+++ exited (status 1) +++
level13@SnowCrash:~
```

This one is pretty obvious, we have to fake the executable we are the user 4242.


## Solution

So we have to pretend we are the user 4242.

Thing is, this user doesn't even exist.

```
level13@SnowCrash:~$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
...
...
...
...
level00:x:2000:2000::/home/user/level00:/bin/bash
level01:x:2001:2001::/home/user/level01:/bin/bash
level02:x:2002:2002::/home/user/level02:/bin/bash
level03:x:2003:2003::/home/user/level03:/bin/bash
level04:x:2004:2004::/home/user/level04:/bin/bash
level05:x:2005:2005::/home/user/level05:/bin/bash
level06:x:2006:2006::/home/user/level06:/bin/bash
level07:x:2007:2007::/home/user/level07:/bin/bash
level08:x:2008:2008::/home/user/level08:/bin/bash
level09:x:2009:2009::/home/user/level09:/bin/bash
level10:x:2010:2010::/home/user/level10:/bin/bash
level11:x:2011:2011::/home/user/level11:/bin/bash
level12:x:2012:2012::/home/user/level12:/bin/bash
level13:x:2013:2013::/home/user/level13:/bin/bash
level14:x:2014:2014::/home/user/level14:/bin/bash
flag00:x:3000:3000::/home/flag/flag00:/bin/bash
flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash
flag02:x:3002:3002::/home/flag/flag02:/bin/bash
flag03:x:3003:3003::/home/flag/flag03:/bin/bash
flag04:x:3004:3004::/home/flag/flag04:/bin/bash
flag05:x:3005:3005::/home/flag/flag05:/bin/bash
flag06:x:3006:3006::/home/flag/flag06:/bin/bash
flag07:x:3007:3007::/home/flag/flag07:/bin/bash
flag08:x:3008:3008::/home/flag/flag08:/bin/bash
flag09:x:3009:3009::/home/flag/flag09:/bin/bash
flag10:x:3010:3010::/home/flag/flag10:/bin/bash
flag11:x:3011:3011::/home/flag/flag11:/bin/bash
flag12:x:3012:3012::/home/flag/flag12:/bin/bash
flag13:x:3013:3013::/home/flag/flag13:/bin/bash
flag14:x:3014:3014::/home/flag/flag14:/bin/bash
```

So we need to find a way to fake UID.

On the web i found this post on this forum : https://www.linuxquestions.org/questions/programming-9/faking-uids-917910/


An user shows us a script that can fake UID, using LD_PRELOAD

But what is LD_PRELOAD ?

A brief post on stackoverflow will explain it all :


```
If you set LD_PRELOAD to the path of a shared object, that file will be loaded before any other library (including the C runtime, libc.so)....

Example :

$ LD_PRELOAD=/path/to/my/executable /bin/ls

Credit : JesperE
```

By undurstanding this trick, you can load a library that will override the getuid() one.

So the goal is to override the getuid() library by another one that will set the UID we want, and then execute the ./level13 executable.


I will be using the script i found on the forum above, and edit it a little.

```sh
#!/bin/bash

THE_USER=$1
THE_UID=`id -u $1`

echo "The User: $THE_USER"
echo "The UID : $THE_UID"

cat << 'EOF' > /tmp/libbecome${THE_USER}.c
int geteuid() {
   return FAKE_UID;
}

int getuid() {
   return FAKE_UID;
}
EOF

gcc -DFAKE_UID=4242 -shared -fPIC -o /tmp/libbecome${THE_USER}.so /tmp/libbecome${THE_USER}.c 
rm /tmp/libbecome${THE_USER}.c # Edited this part so it will give us 4242

export LD_PRELOAD="/tmp/libbecome${THE_USER}.so"

shift 1
$*

rm /tmp/libbecome${THE_USER}.so
```

Since the user 4242 doesn't exist, the script won't be able to give us the 4242 UID, so we edit it and add it in raw.

And we execute it this way

```
level14@SnowCrash:/tmp/C$ bash fakeuid.sh flag13 getflag
The User: flag13
The UID : 3013
Injection Linked lib detected exit..
level14@SnowCrash:/tmp/C$
```

Of course that would be too easy, so lets try the executable.


```sh
level13@SnowCrash:/tmp/C$ bash fakeuid.sh 4242 ~/level13
id: 4242: No such user
The User: 4242
The UID :
UID 2013 started us but we we expect 4242
level13@SnowCrash:/tmp/C$
```

It doesn't work but because the executable runs with extra privilege, so it will always take our real UID.

```sh
level13@SnowCrash:/tmp/C$ bash fakeuid.sh 4242 ltrace ~/level13
id: 4242: No such user
The User: 4242
The UID :
__libc_start_main(0x804858c, 1, 0xbffff7e4, 0x80485f0, 0x8048660 <unfinished ...>
getuid()                                                                  = 4242
strdup("boe]!ai0FB@.:|L6l@A?>qJ}I")                                       = 0x0804b008
printf("your token is %s\n", "2A31L79asukciNyi8uppkEuSx"your token is 2A31L79asukciNyi8uppkEuSx
)                 = 40
+++ exited (status 40) +++
level13@SnowCrash:/tmp/C$
```

When we use a debugger with it, the kernel will not give us extra privilege on that executable, and so it will be runned as our actual UID, which is 4242.

```
level13@SnowCrash:/tmp/C$ su level14
Password:
level14@SnowCrash:~$
```


## What i learned

You can fake UID.

The LD_PRELOAD trick.

You can earn hundred of hours of times by just using google.


