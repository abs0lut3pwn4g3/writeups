---
author: eshaan7
layout: post
---


## Challenge: Cool Image 2 (for 105 points, dynamic) [Forensics]

#### Challenge description: 

	Written by: cppio
	My friend sent me this image, but I can't open it. Can you help me open the image?
	File: cool.png
	Free Hint: Try looking at the raw contents of the file.

<img src="https://raw.githubusercontent.com/abs0lut3pwn4g3/writeups/master/_posts/hsCTFImages/CoolImage2_desc.png" width="300" align="centre"/>

#### Let's capture tis fl4g: 

We have a `cool.png` file, on opening it, we get,

<img src="https://raw.githubusercontent.com/abs0lut3pwn4g3/writeups/master/_posts/hsCTFImages/CoolImage2_openFile.png" width="300" height="300"/> 

Let's see the `hexdump` of this file with the command,

	$ hexdump -C cool.png
  
We get, 

<img src="https://raw.githubusercontent.com/abs0lut3pwn4g3/writeups/master/_posts/hsCTFImages/CoolImage2_hexdump.png" width="900" height="400"/>

As we can see here, there's some string in the header i.e. extra bytes that is messing up our `cool.png` file that's why we are not able to open it in image viewer, <br>
We can simply use `foremost` to extract the image from the messed up file we have.

	$ foremost cool.png
	$ eog output/png/*.png
	
<img src="https://raw.githubusercontent.com/abs0lut3pwn4g3/writeups/master/_posts/hsCTFImages/CoolImage2_flag.png" width="500" height="200"/>

We have the Flag!

	FLAG: hsctf{sorry_about_the_extra_bytes}

#### Thankyou for reading, happy hackin'! ~ [eshaan7](https://eshaan7.cf/)
