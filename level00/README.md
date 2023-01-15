## Level 00

## Clues

First and only clues we have in the demo video of the CTF :

"Find the first file that can be runned by the user flag00."

## Solution

It was just a simple matter of doing a find on everything the user flag00 has.

```sh
find / -user flag00
```

We find a file named 'john' which contains the following :

```
cdiiddwpgswtgt
```

It does not work as a password for the flag00, so we try to decode it using https://www.dcode.fr/.

We found out that its actually a Caesar code, and it translate as :

```
nottoohardhere
```

And its actually our password for the flag00 !


## What i learned

I learned that you could find files that belongs to user in an UNIX environnement.

Also discovered the Caesar code, its still good to know.