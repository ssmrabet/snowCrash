
```
level09@SnowCrash:~$ ls -l
total 12
-rwsr-sr-x 1 flag09 level09 7640 Mar  5  2016 level09
----r--r-- 1 flag09 level09   26 Mar  5  2016 token
level09@SnowCrash:~$ cat -e  token  
f4kmm6p|=M-^B^?pM-^BnM-^CM-^BDBM-^CDu{^?M-^LM-^I$
level09@SnowCrash:~$ ./level09 
You need to provied only one arg.
level09@SnowCrash:~$ ./level09 test
tfuw
level09@SnowCrash:~$ ./level09 aaaaaaaaaaaaaaaaaaaa
abcdefghijklmnopqrst
```
Après un peu d'essais il semblerait que `./level09` affiche `argv[1]` en ajoutant à la valeur ASCII de chaque caractère son index dans la chaine. On peut le simplifier ainsi:
```C
int main(int argc, char **argv) {
	int i = 0;
	while (argv[1][i]) {
		argv[1] += i;
		i++;
	}
	write(1, argv[1], strlen(argv[1]);
}
```
On suppose donc que le fichier `token` est le résultat de `./level09 <token original>`. On va utiliser Python pour reverse l'algorithme et trouver le contenu original.  
Voila une première ébauche de script
```
level09@SnowCrash:~$ python -c "string = 'test'; print(''.join([chr(ord(string[i]) - i) for i in range(len(string))]))"
tdqq
level09@SnowCrash:~$ ./level09 tdqq
test
```
Ca semble fonctionner, maintetant on va utiliser le contenu de `token`:
```
level09@SnowCrash:~$ python -c "string = open('./token', 'r').read(); print(''.join([chr((ord(string[i]) - i) % 256) for i in range(len(string))]))"
f3iji1ju5yuevaus41q1afiuq
```
On s'en sert pour accéder à flag09:
```
level09@SnowCrash:~$ su flag09
Password: 
Don't forget to launch getflag !
flag09@SnowCrash:~$ getflag
Check flag.Here is your token : s5cAJpM8ev6XHw998pRWG728z
```
On a donc le flag `s5cAJpM8ev6XHw998pRWG728z` :tada:
