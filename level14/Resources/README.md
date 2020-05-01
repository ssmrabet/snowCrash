Ici aucun fichier, pour clore le projet nous allons exploiter `getflag` lui-même.
```
flag14@SnowCrash:~$ gdb getflag
(gdb) run
Starting program: /bin/getflag 
You should not reverse this
[Inferior 1 (process 5525) exited with code 01]
```
Premier souci, le programme se rend compte qu'il est dans `gdb`. Il se sert de ptrace pour ca:
```
(gdb) b *0x0804898e
Breakpoint 1 at 0x804898e
(gdb) disas main
...
   0x08048960 <+26>:	xor    eax,eax
   0x08048962 <+28>:	mov    DWORD PTR [esp+0x10],0x0
   0x0804896a <+36>:	mov    DWORD PTR [esp+0xc],0x0
   0x08048972 <+44>:	mov    DWORD PTR [esp+0x8],0x1
   0x0804897a <+52>:	mov    DWORD PTR [esp+0x4],0x0
   0x08048982 <+60>:	mov    DWORD PTR [esp],0x0
   0x08048989 <+67>:	call   0x8048540 <ptrace@plt>
=> 0x0804898e <+72>:	test   eax,eax
   0x08048990 <+74>:	jns    0x80489a8 <main+98>
   0x08048992 <+76>:	mov    DWORD PTR [esp],0x8048fa8
...
(gdb) p $eax
$1 = -1
(gdb) set $eax=1
(gdb) n
Single stepping until exit from function main,
which has no line number information.
Check flag.Here is your token : 
Nope there is no token here for you sorry. Try again :)
0xb7e454d3 in __libc_start_main () from /lib/i386-linux-gnu/libc.so.6
```
En lisant le code desassemblé une partie a retenu notre attention:
```
(gdb) disas main
...
   0x08048afd <+439>:	call   0x80484b0 <getuid@plt>
   0x08048b02 <+444>:	mov    DWORD PTR [esp+0x18],eax
   0x08048b06 <+448>:	mov    eax,DWORD PTR [esp+0x18]
   0x08048b0a <+452>:	cmp    eax,0xbbe
   0x08048b0f <+457>:	je     0x8048ccb <main+901>
   0x08048b15 <+463>:	cmp    eax,0xbbe
   0x08048b1a <+468>:	ja     0x8048b68 <main+546>
   0x08048b1c <+470>:	cmp    eax,0xbba
   0x08048b21 <+475>:	je     0x8048c3b <main+757>
   0x08048b27 <+481>:	cmp    eax,0xbba
   0x08048b2c <+486>:	ja     0x8048b4d <main+519>
...
   0x08048bb6 <+624>:	cmp    eax,0xbc6
   0x08048bbb <+629>:	je     0x8048de5 <main+1183>
   0x08048bc1 <+635>:	jmp    0x8048e06 <main+1216>
...
```
Allons voir de plus près ce qu'il s'y passe:
```
(gdb) b *0x08048b0a
Breakpoint 2 at 0x8048b0a
(gdb) n
Single stepping until exit from function main,
which has no line number information.

Breakpoint 2, 0x08048b0a in main ()
(gdb) p $eax
$1 = 2014
(gdb) p 0xbbe
$2 = 3006
(gdb) p 0xbba
$3 = 3002
(gdb) p 0xbc6
$4 = 3014
```
Il semblerait que `$eax` aie la valeur de notre UID et soit comparé a une série de valeurs, toutes dans le range 3001-3014...  
On se rappelle du fichier `/etc/passwd`, les UID ne sont pas laissés au hasard. Pour chaque niveau `xx` il existe un utilisateur `levelxx` avec un `uid = 20xx` et un autre utilisateur `flagxx` avec un `uid = 30xx`.

```
level14@SnowCrash:~$ id
uid=2014(level14) gid=2014(level14) groups=2014(level14),100(users)
```
On va donc remplacer la valeur de `$eax` par `3014` pour se faire passer pour `level14` !
```
; toujours sur breakpoint 2
(gdb) set $eax=3014
(gdb) n  
Single stepping until exit from function main,
which has no line number information.
Check flag.Here is your token : 7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ
0xb7e454d3 in __libc_start_main () from /lib/i386-linux-gnu/libc.so.6
```
On a eu le flag, `7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ` :tada:

PS: Le flag fonctionne aussi pour se connecter avec flag14:
```
level14@SnowCrash:~$ su flag14
Password: 
Congratulation. Type getflag to get the key and send it to me the owner of this livecd :)
flag14@SnowCrash:~$ getflag
Check flag.Here is your token : 7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ
```
