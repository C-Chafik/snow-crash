## Level 09


## Clues

Another duo of executable and a token file
```
level09@SnowCrash:~$ ls -l
total 12
-rwsr-sr-x 1 flag09 level09 7640 Mar  5  2016 level09
----r--r-- 1 flag09 level09   26 Mar  5  2016 token
level09@SnowCrash:~$
```

This time we can read the token file :

```
level09@SnowCrash:~$ cat token
f4kmm6p|=�p�n��DB�Du{��
level09@SnowCrash:~$
```

It seems corrupted, lets check the executable

```
level09@SnowCrash:~$ ./level09 a
a
level09@SnowCrash:~$ ./level09 abc
ace
level09@SnowCrash:~$ ./level09 abcdefg
acegikm
level09@SnowCrash:~$ ./level09 123
135
level09@SnowCrash:~$ ./level09 1235
1358
level09@SnowCrash:~$ ./level09 1236
1359
level09@SnowCrash:~$ 
```

After a few minutes of investigation, we see that this executable is actually a sort of rot1.

It incremente every character in the string by its index, so for example:

```
In "abc"

a += 0 = 'a'

b += 1 = 'c'

c += 2 = 'e'

Which give us "ace"
```

Lets give the token to the executable but obviously it would be too easy :

```
level09@SnowCrash:~$ ./level09 "f4kmm6p|=�p�n��DB�Du{��"
f5mpq;v�E���|���~����[��`������
level09@SnowCrash:~$
```

It just make it more worse....

So starting to this point we just need to do a brainwashing and try to figure out what is the way to decode this token.


After trying a lot of decoding method (rot1, rot13, and the same decoding method of the executable) i found something.

Maybe the token we have, have already been trough the executable, so lets try to do it the other way around.

Instead of incrementing by the index, we are going to decremente this way :

```
In "zyx"

z -= 0 = 'z'

y -= 1 = 'x'

x -= 2 = 'v'

Which give us "zxv"

```


Lets write it in python.


```
level09@SnowCrash:~$ cd /tmp
level09@SnowCrash:/tmp$ vim script.py
```

The script is the following :
```py
import sys

def main(str):
        index = 0
        for char in str:
                char = chr(ord(char) - index)
                print(char)
                index = index + 1

main(sys.argv[1])
```

Lets try it :

```
level09@SnowCrash:/tmp$ python script.py $(cat ~/token)
f
3
i
j
i
1
j
u
5
y
u
e
v
a
u
s
4
1
q
1
a
f
i
u
q
```

Which give us the following token : ```f3iji1ju5yuevaus41q1afiuq```


```
level09@SnowCrash:/tmp$ su flag09
Password: 
Don't forget to launch getflag !
flag09@SnowCrash:~$ getflag
Check flag.Here is your token : s5cAJpM8ev6XHw998pRWG728z
flag09@SnowCrash:~
```

And here we go !


## What i learned

A little bit of python scripting (Now that i see the use its much more easy to write python)

Persicacity

More curiosity