## Level 08

## Clues

```
level08@SnowCrash:~$ ls -l
total 16
-rwsr-s---+ 1 flag08 level08 8617 Mar  5  2016 level08
-rw-------  1 flag08 flag08    26 Mar  5  2016 token
level08@SnowCrash:~$
```

We have an executable to trick, and a token file

```
level08@SnowCrash:~$ cat token 
cat: token: Permission denied
level08@SnowCrash:~$ file token
token: regular file, no read permission
level08@SnowCrash:~$ 
```

We cannot do anything with that file, so it might contain our flag inside.

Lets see what the executable does :

```
level08@SnowCrash:~$ ./level08 token
You may not access 'token'
level08@SnowCrash:~$ ./level08 blabla
level08: Unable to open blabla: No such file or directory
level08@SnowCrash:~$ touch /tmp/file.txt
level08@SnowCrash:~$ echo "hehe" > /tmp/file.txt
level08@SnowCrash:~$ ./level08 /tmp/file.txt
hehe
```

So this executable takes a file path in argument, open it and prints out its content.

When we give it the token file, it says "You may not access token"

Maybe the executable checks our right before opening the file ?

Lets see what it is really doing :

```
level08@SnowCrash:~$ ltrace ./level08 
printf("%s [file to read]\n", "./level08"./level08 [file to read]) = 25
exit(1 <unfinished ...>


level08@SnowCrash:~$ ltrace ./level08 blabla
strstr("blabla", "token") = NULL
open("blabla", 0, 014435162522)  = -1
err(1, 0x80487b2, 0xbffff91e, 0xb7fe765d, 0xb7e3ebaflevel08: Unable to open blabla: No such file or directory


level08@SnowCrash:~$ ltrace ./level08 token
strstr("token", "token")= "token"
printf("You may not access '%s'\n", "token"You may not access 'token') = 27


level08@SnowCrash:~$ ltrace ./level08 /tmp/file.txt
strstr("/tmp/file.txt", "token") = NULL
open("/tmp/file.txt", 0, 014435162522) = 3
read(3, "hehe\n", 1024) = 5
write(1, "hehe\n", 5) = 5
```

If we take a closer look, there is an obvious behavior.

When you give it no argument it only prints out an error message.

When you give it a file that doesn't exist, it will compare using strstr the name of the file, with "token" and try to open it.

So if we give him "token" as a filename, the strstr function will return a positive pointer, and will print out that we cannot access the file named token.

And finally when we give him a real file that is not named token, it will open it, read it and print the content.


This reverse engineering process showed us that the program doesn't check our ID in order to check that we can or can't open the token file, it only use the filename !


## Solution

Since the program will never open a file a named "token", the only way to make it open a file is by using another name.

Lets try with a symbolic link.


```
level08@SnowCrash:~$ cd /tmp
level08@SnowCrash:/tmp$ ln -s ~/token ImNotAToken
level08@SnowCrash:/tmp$ cd
level08@SnowCrash:~$ ./level08 /tmp/ImNotAToken
quif5eloekouj29ke0vouxean
```

Once again we move to /tmp in order to create our file.

And its working ! We have our flag.

```
level08@SnowCrash:~$ su flag08
Password: 
Don't forget to launch getflag !
flag08@SnowCrash:~$ getflag
Check flag.Here is your token : 25749xKZ8L7DkSCwJkT9dyv6f
flag08@SnowCrash:~$ 
```

## What i learned

More reverse engineering experience

More curiosity