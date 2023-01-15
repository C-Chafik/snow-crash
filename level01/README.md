## Level 01



## Clues

This time, there is no file given to us, we must explore the session and see what we can crack.

We are looking for the flag01's password, we lets look at the /etc/passwd file.

```
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

We can see that there is a password in the flag01 user information.

It does not work as a password, so it must be hashed, maybe we can crack the password for it.


## Solution

Since we know there is a hashed password for the flag01 user, we are going to crack it using John The Reaper. (https://www.openwall.com/john/)

We put the following line :

```
flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash
```

Into his own file, and we give it to john.


```sh
flag01:abcdefg:3001:3001::/home/flag/flag01:/bin/bash

1 password hash cracked, 0 left
```

And now "42hDRfypTqqnw" become "abcdefg".

This password work as a password for the flag01 !

```
level01@SnowCrash:~$ su flag01
Password:
Don't forget to launch getflag !
flag01@SnowCrash:~$
```

## What i learned

You can crack some hashed password with John The Reaper.

Password, or hashed password can be stored at the second entry in the /etc/passwd file in an UNIX environnement.

More curiosity in my brain.