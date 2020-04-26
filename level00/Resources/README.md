# LEVEL 00
Au début on a chercher les fichiers que le user flag00 posséde

```
find / -user flag00  2>/dev/null
```

find pour la recherche dans le dossier racine /
-user : File is owned by user uname
2>/dev/null pour éliminer les erreurs du retour (puisqu'il va surement afficher pleins d'erreurs de permission denied sur les dossiers qui ne posséde pas)

Après j'ai affiché le fichier de retour

```
cat /usr/sbin/john
cdiiddwpgswtgt
```

Donc normalement le mot de passe est cdiiddwpgswtgt sauf que j'ai un message d'erreur Authentification failure

D'après les resource de la video y'a un lien de cryptage/decryptage dcode.fr où le code César est décrit et c'est ce que j'ai utilisé

On a testé tout les chiffre césar pour le decode jusqu'à ce qu'on a trouvé que 15 est le bon et le mot de passe est

```
nottoohardhere
```
