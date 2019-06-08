---
layout: post
author: X3eRo0
--- 
### Challenge: License [ 298(DYN) RE ] 




<div id="maincontent">
<div id="outputFigDisplay" class="fig">
	<pre id="taag_output_text" class="fig" contenteditable="true">
		    __  _______ __________________              ___   ____ _______ 
		   / / / / ___// ____/_  __/ ____/             |__ \ / __ &lt;  / __ \
		  / /_/ /\__ \/ /     / / / /_       ______    __/ // / / / / /_/ /
		 / __  /___/ / /___  / / / __/      /_____/   / __// /_/ / /\__, / 
		/_/ /_//____/\____/ /_/ /_/                  /____/\____/_//____/  
	                                                                   </pre>
<div>&nbsp;</div>
</div>
</div>
              
              
                                          298 - DYNAMIC - RE
                                          
                                          
Challenge Description:
 


        Written by: ItzSomebody

	Description: 
	Keith made a cool license-checking program but he forgot the flag he used to create the key! To make matters worse, he lost the source code and stripped the binary for his license-generator program. Can you help Keith recover his flag? All he knows is:
	• The license key is 4-EZF2M-7O5F4-V9P7O-EVFDP-E4VDO-O
	• He put his name (in the form of 'k3ith') as the first part of the flag
	• There are 3 underscores
	• The flag is in the format hsctf{}
	• The flag doesn't have random character sequences (you should be able to read the entire flag easily).
	• The flag only contains lowercase English letters and numbers.
	• The generator might produce the same keys for different inputs because Keith was too lazy to write the algorithm properly.

Binary : [license](https://github.com/X3eRo0/X3eRo0.github.io/raw/master/license)
              
before opening the binary in any disassembler or any debugger, i thought lets take a look at what it does.

runing the binary will make your work a little easier in this case.

```
╭─pulkit@pulkit-ER28-0652 ~/Capture_The_Flag/hsctf 
╰─$ ./license 
gimme dat string: A
generating key for: a
X
╭─pulkit@pulkit-ER28-0652 ~/Capture_The_Flag/hsctf 
╰─$ ./license
gimme dat string: B
generating key for: b
Y
╭─pulkit@pulkit-ER28-0652 ~/Capture_The_Flag/hsctf 
╰─$ ./license
gimme dat string: C
generating key for: c
Z
```

you can clearly see the pattern. each letter is simply substituted by some other letter.

so what we can do in this case is that we can get a python dictionary which will store which charecter is substituted by which letter.
once we have that dictionary we resubstitute the charecters in the key given to us.

i made a script for that
```
from pwn import *
import string
context.log_level = "critical"

flag_license = "4-EZF2M-7O5F4-V9P7O-EVFDP-E4VDO-O".replace("-","")

chars = {}

for i in range(0x21,0x7f):
	p = process("./license")
	p.recvuntil("gimme dat string: ")
	p.sendline(chr(i))
	corchar = p.recv(23)[22:]
	
	chars[corchar] = chr(i)
	p.close

flag = ""

for ch in flag_license:
	flag += chars[ch]

print flag
```

running this script gives us partial flag

```
╭─pulkit@pulkit-ER28-0652 ~/Capture_The_Flag/hsctf 
╰─$ python find_keys.py 
hsctf{k}ith_m~k}s_tr~sh_r}}
```

we can see that "}" is also 3 and "~" is 4
in the end flag comes out to be
```
hsctf{k3ith_m4k3s_tr4sh_r3}
```
  


Thanks for reading that.
                                                                                                ~by [X3eRo0](https://twitter.com/X3eRo0)
