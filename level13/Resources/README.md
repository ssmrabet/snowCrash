
```
level13@SnowCrash:~$ ls -l 
total 8
-rwsr-sr-x 1 flag13 level13 7303 Aug 30  2015 level13
level13@SnowCrash:~$ ./level13 
UID 2013 started us but we we expect 4242
```
On remarque que 2013 est notre UUID:
```
level13@SnowCrash:~$ id  
uid=2013(level13) gid=2013(level13) groups=2013(level13),100(users)
```
On se demande alors sous quel ID il faut executer le programme:
```
level13@SnowCrash:~$ cat /etc/passwd | grep 4242
```
Il n'y a pas d'utilisateur 4242, allons voir dans gdb:
```gdb
level13@SnowCrash:~$ gdb ./level13
(gdb) disas main
Dump of assembler code for function main:
   0x0804858c <+0>:	push   ebp
   0x0804858d <+1>:	mov    ebp,esp
   0x0804858f <+3>:	and    esp,0xfffffff0
   0x08048592 <+6>:	sub    esp,0x10
   0x08048595 <+9>:	call   0x8048380 <getuid@plt> ; on assigne $eax a guid
   0x0804859a <+14>:	cmp    eax,0x1092 ; on compare guid a 0x1092
   0x0804859f <+19>:	je     0x80485cb <main+63> ; s'ils sont egaux on saute a +63, sinon on continue et on tombera sur un exit
   0x080485a1 <+21>:	call   0x8048380 <getuid@plt>
   0x080485a6 <+26>:	mov    edx,0x80486c8
   0x080485ab <+31>:	mov    DWORD PTR [esp+0x8],0x1092
   0x080485b3 <+39>:	mov    DWORD PTR [esp+0x4],eax
   0x080485b7 <+43>:	mov    DWORD PTR [esp],edx
   0x080485ba <+46>:	call   0x8048360 <printf@plt>
   0x080485bf <+51>:	mov    DWORD PTR [esp],0x1
   0x080485c6 <+58>:	call   0x80483a0 <exit@plt> ; fin du chemin par defaut (guid != 4242)
   0x080485cb <+63>:	mov    DWORD PTR [esp],0x80486ef ; Partie que l'on veut explorer
   0x080485d2 <+70>:	call   0x8048474 <ft_des>
   0x080485d7 <+75>:	mov    edx,0x8048709
   0x080485dc <+80>:	mov    DWORD PTR [esp+0x4],eax
   0x080485e0 <+84>:	mov    DWORD PTR [esp],edx
   0x080485e3 <+87>:	call   0x8048360 <printf@plt>
   0x080485e8 <+92>:	leave  
   0x080485e9 <+93>:	ret    
End of assembler dump.
(gdb) p 0x1092
$1 = 4242 ; 0x1092 vaut 4242, la valeur de reference
```
On va donc mettre un breakpoint juste avant la comparaison du guid et changer la valeur de `$eax` pour mettre `4242` et voir ce qu'il se passe:
```gdb
(gdb) b *0x0804859a
Breakpoint 1 at 0x804859a
(gdb) disas main
...
   0x08048595 <+9>:	call   0x8048380 <getuid@plt>
=> 0x0804859a <+14>:	cmp    eax,0x1092
   0x0804859f <+19>:	je     0x80485cb <main+63>
...
(gdb) p $eax 
$1 = 2013
(gdb) p 0x1092
$2 = 4242
(gdb) set $eax = 4242
(gdb) p $eax
$3 = 4242
(gdb) n   
Single stepping until exit from function main,
which has no line number information.
your token is 2A31L79asukciNyi8uppkEuSx
0xb7e454d3 in __libc_start_main () from /lib/i386-linux-gnu/libc.so.6
```
C'était finalement très simple ! Le flag est `2A31L79asukciNyi8uppkEuSx` :tada:
