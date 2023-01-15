## Level 00

## Clues

First and only clues we have in the demo video of the CTF :

"Find the first file that can be runned by the user flag00."

## Solution

It was just a simple matter of doing a find on everything the user flag00 has.

```sh
find / -user flag00 2> /dev/null // So we print only good occurence
```

We find a file named 'john' :
```
level00@SnowCrash:~$ find / -user flag00 2> /dev/null
/usr/sbin/john
/rofs/usr/sbin/john
```

Which contains the following :
```
cdiiddwpgswtgt
```

It does not work as a password for the flag00, so we try to decode it using https://www.dcode.fr/.

We found out that its actually a Caesar code, and it translate as :

```
nottoohardhere
```

And its actually our password for the flag00 !
```
level00@SnowCrash:~$ su flag00
Password:
Don't forget to launch getflag !
flag00@SnowCrash:~$
```


## What i learned

I learned that you could find files that belongs to user in an UNIX environnement.

Also discovered the Caesar code, its still good to know.