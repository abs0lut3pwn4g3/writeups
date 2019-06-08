---
author: eshaan7
layout: post
---


### Challenge: Get Schwifty ?(for 150 points, dynamic) [Forensics]

#### Challenge description: 

	Evil Morty, the first democratically-elected President of the Citadel of Ricks, has killed off twenty-seven known Ricks from various dimensions, as well as capturing, torturing, and enslaving hundreds of Mortys. As a fellow Rick-less Morty, Investigator Rick gives you a file revealing Evil Morty's past and true nature. However he cannot seem to access it. Can you help recover it to stop Evil Morty ? 
	Download link here: [link](https://drive.google.com/file/d/1XxqRFFpn9Zj7gNQ6Ahg74rTAFPbkeNq9/view?usp=drive_open)
	Author: maskofmydisguise


#### Let's capture tis fl4g: 

We are given a `GetSchwifty.7z` file, we extract it to find a mountable disk image `GetSchwifty.7z`.
On mounting it we see that it is empty.

<img src="https://raw.githubusercontent.com/abs0lut3pwn4g3/writeups/master/_posts/GetSchwifty_mounted_image.png">

So we need to analyze this disk image and try to recover deleted data, if any. 
We can use [testdisk, data recovery tool](https://www.cgsecurity.org/wiki/TestDisk) for this.

	┌─[eshaan@HP-ubuntu]-[~/Desktop/CTFs/Abs0lut3Pwn4g3_CTF/encrypt-ctf-2019/Forensics/150_Get_Schwifty/files_in_img/tmp]
    └─➤ ls
	GetSchwifty.7z  GetSchwifty.img
	┌─[eshaan@HP-ubuntu]-[~/Desktop/CTFs/Abs0lut3Pwn4g3_CTF/encrypt-ctf-2019/Forensics/150_Get_Schwifty/files_in_img/tmp]
	└─➤ sudo testdisk GetSchwifty.img

<img src="https://raw.githubusercontent.com/abs0lut3pwn4g3/writeups/master/_posts/GetSchwifty_testdisk1.png">
<img src="https://raw.githubusercontent.com/abs0lut3pwn4g3/writeups/master/_posts/GetSchwifty_testdisk2.png">

Here we see that we get a few files,

1. hahahaha
2. secret_hidden_file.png
3. 'school work(do not open)' (directory)
4. super_duper_secret_hidden_file.txt

here (secret_hidden_file.png)[] has the flag in plainsight.

	FLAG: encryptCTF{alw4ys_d3lete_y0ur_f1les_c0mpletely}

Also, (super_duper_secret_hidden_file.txt)[] contains a base64 string that decodes to `not_the_flag_LOL`. 
Also, (school work(do not open))[] had a random audio file.

*Just some rabbit holes for the lighthearted ;)*

#### BONUS:

the (hahahaha)[] file was actually a broken `.zip` file which had to be fixed using `hexedit` to set the correct <a href="https://en.wikipedia.org/wiki/File_format#Magic_number">magic bytes</a>. Which contains a 2nd flag, which I forgot to implement. sorry xD


#### Thankyou for reading, happy hackin'! ~ [eshaan7](https://eshaan7.cf/)
