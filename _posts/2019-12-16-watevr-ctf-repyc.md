---
layout: post
author: X3eRo0
--- 
### Challenge: REPYC [REV]

<pre id="user-content-taag_output_text">
                      _      _____ ___________   _____    _______________  ___  ___ ______ 
                     | | /| / / _ /_  __/ __/ | / / _ \  / ___/_  __/ __/ |_  |/ _ <  / _ \
                     | |/ |/ / __ |/ / / _/ | |/ / , _/ / /__  / / / _/  / __// // / /\_, /
                     |__/|__/_/ |_/_/ /___/ |___/_/|_|  \___/ /_/ /_/   /____/\___/_//___/ 
                                                                                           
</pre>

                                                 147 Points - REV

Challenge Description:
 

> woo thi chal sooo repyc!<br>
> **file**: [3nohtyp.pyc](https://github.com/abs0lut3pwn4g3/writeups/blob/master/_posts/watevr_ctf_images/3nohtyp.pyc?raw=true)<br>
> **Difficulty estimate**: Easy

We are given a pyc file and running file on this pyc reveals that its a python 3.6 compiled python bytecode.

```
[~]$ file 3nohtyp.pyc                                                                              
3nohtyp.pyc: python 3.6 byte-compiled
```

so i went with the usuall approach to a python pyc challenge, where the first part is to decompile the bytecode.
i used uncompyle6 ```[~]$ uncompyle6 3nohtyp.pyc > repyc.py``` and the output was very weird just look at this [file](https://pastebin.com/h8dM34Jt)

![repyc.py](https://github.com/abs0lut3pwn4g3/writeups/raw/master/_posts/watevr_ctf_images/repyc.py.PNG)

I never thought that reversing a python source file would be so difficult. At first i thought maybe there is 
python obfuscator which obfuscates variable names with unicode Chinese looking charecters and there are a lot of other obfuscators which uses other techniques similar to this one but unfortunately i didn't find any obfuscator which does this kind of obfuscation, i thought if i found the obfuscator maybe an unobfuscator also exists so that will make my work easier but i was wrong.

Some good obfuscators which i found are: 

 - [PyObfx](https://github.com/PyObfx/PyObfx)
 - [PythonObfuscator](https://github.com/lhr0909/PythonObfuscator)

So the next thing i did was i started replacing each chinese charecter in the file with simple names like i 
translated first 3 lines like this

```
佤 = 0     
侰 = ~佤 * ~佤
俴 = 侰 + 侰      
```

translated to 

```
A = 0
B = ~A * ~A
C = B + B
```

and finally

```
A = 0
B = 1
C = 2
```

And later in the code instead of just using 1, 2 and 3 they use A, B and C so i replaced all the A, B and Cs with their respective values

then comes this function

```def 䯂(䵦):```

At this point i already guessed it was a VM so i named this function VM(byte)

next we see some more declarations which turns out to be registers and memory and stack and stuff
for example 

```괠 = [A] * C ** (C * C)``` translates to ```reg = [0]  * 16```
and ```궓 = [A] * 100``` translates to ```mem = [0] * 100```
and after reading the rest of the code turns out ```괣 = []``` was the stack

so this part looks like this now
```
A = 0
B = 1
C = 2

def VM(byte):
    var1 = 0
    var2 = 0
    reg  = [0] * 16 # array of registers
    mem = [0] * 100 # contains our input
    stack = []
```

then there is a while loop ```while 䵦[굴][A] != '듃':``` which translated to ```byte[var1][0] != '듃' ```
so it was clear that '듃' is the hlt instruction as soon as it encounters '듃' the VM will stop execution

and finally 

```
굸 = 䵦[굴][A].lower()
亀 = 䵦[굴][B:]
```

translated to 
```
opcode = byte[var1][0].lower()
_arg_ = byte[var1][1:]
```

and at this point it was time to analyse all the opcodes, so i used my big brain and only analysed the ones which were actually executed. when you see the call to the VM() function the arguments passed is a list of instructions for sat down and started to do find and replace on all the instructions. 
but then i noticed that the instructions in the bottom were not correctly indented so i fixed that and i ended up with this

![solution.py](https://github.com/abs0lut3pwn4g3/writeups/raw/master/_posts/watevr_ctf_images/solution.py.PNG)

after that i just did a lot of find and replace and i finally fixed the whole python file
and i ended up with this VM Bytecode

```
VM([
 ['mov', 0, 'Authentication token: '],
 ['mem_mov', 0, 0], # mem(r1) = r2
 ['mov', 6, 'á×äÓâæíäàßåÉÛãåäÉÖÓÉäàÓÉÖÓåäÉÓÚÕæïèäßÙÚÉÛÓäàÙÔÉÓâæÉàÓÚÕÓÒÙæäàÉäàßåÉßåÉäàÓÉÚÓáÉ·Ôâ×ÚÕÓÔÉ³ÚÕæïèäßÙÚÉÅä×ÚÔ×æÔÉ×Úïá×ïåÉßÉÔÙÚäÉæÓ×ÜÜïÉà×âÓÉ×ÉÑÙÙÔÉâßÔÉÖãäÉßÉæÓ×ÜÜïÉÓÚÞÙïÉäàßåÉåÙÚÑÉßÉàÙèÓÉïÙãÉáßÜÜÉÓÚÞÙïÉßäÉ×åáÓÜÜ\x97ÉïÙãäãÖÓ\x9aÕÙÛ\x99á×äÕà©â«³£ï²ÕÔÈ·±â¨ë'],
 ['mov', 2, 120],
 ['mov', 4, 15],
 ['mov', 3, 1],
 ['multiply', 2, 2, 3], # r2 = r2 * r3
 ['add', 2, 2, 4], # r2 = r2 + r4
 ['nop', 0, 2], # maybe nop
 ['zero_reg', 3], # sets r3 = 0
 ['xor_string', 6, 3],
 ['mov', 0, 'Thanks.'],
 ['mov', 1, 'Authorizing access...'],
 ['print_reg', 0], # prints r0
 ['reg2mem', 0, 0], # set r[arg0] = mem[arg1]
 ['xor_string', 0, 2],
 ['subtract_string', 0, 4],
 ['mov', 5, 19],
 ['strcmp', 0, 6, 5], # check strings ; if not equal r[7] = 1 ; push r[arg[2]]
 ['print_reg', 1],
 ['mov', 1, 'Access denied!'],
 ['print_reg', 1],
 ['hlt']])
```

clearly it loads this huge string into r6 and then xors it with 135 and then subtract 15 from each charecter and then compares with our input so i quicly wrote a decrypt function

```
def find_flag():

    b = 'á×äÓâæíäàßåÉÛãåäÉÖÓÉäàÓÉÖÓåäÉÓÚÕæïèäßÙÚÉÛÓäàÙÔÉÓâæÉàÓÚÕÓÒÙæäàÉäàßåÉßåÉäàÓÉÚÓáÉ·Ôâ×ÚÕÓÔÉ³ÚÕæïèäßÙÚÉÅä×ÚÔ×æÔÉ×Úïá×ïåÉßÉÔÙÚäÉæÓ×ÜÜïÉà×âÓÉ×ÉÑÙÙÔÉâßÔÉÖãäÉßÉæÓ×ÜÜïÉÓÚÞÙïÉäàßåÉåÙÚÑÉßÉàÙèÓÉïÙãÉáßÜÜÉÓÚÞÙïÉßäÉ×åáÓÜÜ\x97ÉïÙãäãÖÓ\x9aÕÙÛ\x99á×äÕà©â«³£ï²ÕÔÈ·±â¨ë'
    temp = ''
    for i in range(len(b)):
        temp += chr(ord(b[i]) + 15)

    a = temp
    temp = ""
    for i in range(len(a)):
        temp += chr(ord(a[i]) ^ 135)

    print(temp)
```

and just after that i got the flag

```
[~]$ python3 solution.py                                                                           
watevr{this_must_be_the_best_encryption_method_evr_henceforth_this_is_the_new_Advanced_Encryption_Standard_anyways_i_dont_really_have_a_good_vid_but_i_really_enjoy_this_song_i_hope_you_will_enjoy_it_aswell!_youtube.com/watch?v=E5yFcdPAGv0}
```




damn that flag is huge and i also wonder whats up with these youtube links?


Thanks for reading that. ~by [X3eRo0](https://twitter.com/X3eRo0)

