# LEVEL 02
dans ce level on trouve un fichier pcap
### pcap is an application programming interface (API) for capturing network traffic
comme la video l'indique on va utiliser un lecteur pcap

on copie le fichier du serveur vers local

```
scp -P 4242 level02@192.168.1.23:level02.pcap .
```

on installe wireshark, on met le fichier dans ce logiciel on trouve que le paquet 43 contient le mot Password on ouvre le flux TCP de ce paquet on trouve le password

```
Password: ft_wandr...NDRel.L0L
```

Ce mot de passe n'est pas valide, on essaie donc de le voir en hexdump on trouve que le . = 7f et avec un man ascii on peut voir que 7f = DEL donc on elimine le nombre de points en lettres

```
Password: ft_waNDReL0L
```
