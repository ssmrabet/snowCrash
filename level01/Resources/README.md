# LEVEL 01
on va afficher le contenu de passwd

```
cat /etc/passwd
```

la ligne qui nous intéresse est qui affiche le password du flag01

```
flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash
```

on va copier cette ligne dans un fichier et utiliser john pour afficher le password
### John the Ripper is a fast password cracker

```
./john password_file

Warning: detected hash type "descrypt", but the string is also recognized as "descrypt-opencl"
Use the "--format=descrypt-opencl" option to force loading these as that type instead
Using default input encoding: UTF-8
Loaded 1 password hash (descrypt, traditional crypt(3) [DES 256/256 AVX2-16])
Will run 12 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
abcdefg          (flag01)
1g 0:00:00:00 DONE 2/3 (2020-04-23 11:38) 100.0g/s 4979Kp/s 4979Kc/s 4979KC/s 123456..lucky0
Use the "--show" option to display all of the cracked passwords reliably
Session completed
```

donc on conclu que le mot de passe que john nous affiche est 

```
abcdefg
```

on utilise ce mot de passe pour ce connecter a flag01 et faire getflag par la suite
