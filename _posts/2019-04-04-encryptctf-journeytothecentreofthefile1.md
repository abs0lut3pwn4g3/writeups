o---
author: eshaan7
layout: post
---


### Challenge: Journey to the centre of the file 1(for 75 points, static) [Forensics]

#### Challenge description: 

	"Nearly everything is really interesting if you go into it deeply enough …" - Richard Feynman
	Author: maskofmydisguise
	file : (ziptunne1.gz)[]

#### Let's capture tis fl4g: 

We are given a `ziptunne1.gz` file which as the name says is `gzipped` again and again.

I don't know if someone tried this manually, but seriously like um don't do that?
We are hackers, let the computer do the manual labour for us!
So we write a shell script. ez pz 
<sub><sup>I use range 1-100 cz we don't know how many stages of archiving is there. So just a guess.</sup></sub>

```bash
#!/bin/bash

mkdir tmp
cp ziptunnel1.gz tmp/ # creating tmp directory and solving there coz we use `ls` to get filenames
cd tmp/
for i in {1..100}
do
	if [ $((i%2)) -eq 0 ]
   	then
   		gzip -d *
	else
		unzip *
		rm *.zip > /dev/null 2>&1
	fi
done
```

As the script loops over every stage, it finally hits a stop when we get a `flag.txt` which contains the flag! ez pz

	FLAG: encryptCTF{w422up_b14tch3s}





