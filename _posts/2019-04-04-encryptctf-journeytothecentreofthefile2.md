---
author: eshaan7
layout: post
---


### Challenge: Journey to the centre of the file 2(for 150 points, static) [Forensics]

#### Challenge description: 
	
	This challenge unlocks after completing "Journey to the centre of the file 1" challenge.

	[_Improvise. Adapt. Overcome_](https://imgur.com/gallery/wMbXB "Improvise. Adapt. Overcome" )
	file: (ziptunnel2)[ ]
	Author: maskofmydisguise

#### Let's capture tis fl4g: 

We are given a `ziptunnel2` file here. This is level 2 of (Journey to the centre of the file 1)[ ] Challenge.
Here, instead of just `gzip` we have various types of compression at random stages!
But, as the challenge description says we gotta *Improvise. Adapt. Overcome*
So let's make some changes to our `get_flag.sh` script.

```bash
#!/bin/bash

mkdir tmp/
cp ziptunnel2 tmp/ # creating tmp directory and solving there coz we use `ls` to get filenames
cd tmp/
filename=$(ls)
rm -r tmp2/
mkdir tmp2/
cp $filename tmp2/
cd tmp2/
filename=$(ls)
while [ 1 ]
do
	file $filename
	file $filename | grep "bzip2"
	if [ "$?" -eq 0 ]
	then
		mv $filename $filename.bz2
		bunzip2 $filename.bz2 > /dev/null
		filename=$(ls)	
	fi

	file $filename | grep "Zip"
	if [ "$?" -eq 0 ]
	then
		mv $filename $filename.zip
		unzip $filename.zip > /dev/null
		rm $filename.zip > /dev/null 2>&1
		filename=$(ls)	
	fi

	file $filename | grep "gzip"
	if [ "$?" -eq 0 ]
	then
		mv $filename $filename.gz
		gunzip $filename.gz > /dev/null
		filename=$(ls)
	fi

	file $filename | grep "ASCII"
	if [ "$?" -eq 0 ]
	then
		echo 
		echo
		echo "Flag Found!"
		echo
		cat $filename
		echo
		echo
		break
	fi
done
```

This script directly spits out the flag, 

	FLAG: encryptCTF{f33ls_g00d_d0nt_it?}

Now, one thing is that I am not gonna explicitly explain every line of the code, cz (John Hammond)[https://www.youtube.com/user/RootOfTheNull/] actually has done a (writeup video)[https://www.youtube.com/watch?v=wRSwagjvSqU&t=2s] on a similar challenge.

*YeAh, I know what you thinkin' it was copied. OMG!*