# LEVEL 04
On a un fichier Perl level04.pl où on trouve que le script s'execute en port 4747 et il va afficher avec echo le resultat de la commande envoyée

On va faire la méme chose que level03 on execute getflag avec ce script perl en utilisant curl et mettre getflag comme variable x

```
curl 192.168.1.16:4747?x=\`getflag\`
```
