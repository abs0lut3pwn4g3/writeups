---
author: eshaan7
layout: post
---


### Challenge: TheKey(for 100 points) [Forensics | BsidesSF 2019 CTF]

We are given a  `thekey.pcapng` packet file. On analyzing with wireshark, we see that we have a lot of usb data on this pcap file. <br/>
let´s extract the Leftover Capture Data with `tshark`

	tshark -r thekey.pcapng -T fields -e usb.capdata

<img src="https://raw.githubusercontent.com/Eshaan7/CTFs_datadumps_2019/master/BSidesSF_CTF_2019_WithWriteups/forensics/100_thekey_COMPLETE/pics/1.png">

we need only numbers without zeros and separators

	tshark -r thekey.pcapng -T fields -e usb.capdata |  grep -v '00:00:00:00:00:00:00:00' | cut -d ':' -f3

<img src="https://raw.githubusercontent.com/Eshaan7/CTFs_datadumps_2019/master/BSidesSF_CTF_2019_WithWriteups/forensics/100_thekey_COMPLETE/pics/2.png">

save this to `usb_keys.txt`

using sublime, I removed the newline chars converted the keys into hex value and basically a python list (regex are awesome!)

	keys = [ "0x19", "0x0C", "0x0C", "0x10", "0x2C", "0x09", "0x0"...............

<br/>
Next, on the <a href="https://www.usb.org/sites/default/files/documents/hut1_12v2.pdf">pdf</a> (Page 53), we have a key map of a USB Keyboard

<img src="https://raw.githubusercontent.com/Eshaan7/CTFs_datadumps_2019/master/BSidesSF_CTF_2019_WithWriteups/forensics/100_thekey_COMPLETE/pics/usbDoc.png">

I had to manually copy & paste the key bindings and then scrap it using `tr -d " " -f1` and sublime(again, learn regex!) and converted it to another python list!

	{ "0x00" :"",
	"0x04" :"a",
	"0x05" :"b",
	"0x06" :"c",
	"0x07" :"d",
	... }

but wait, we have too much data, let’s use a python script to do the work for us! :D

<img src="https://raw.githubusercontent.com/Eshaan7/CTFs_datadumps_2019/master/BSidesSF_CTF_2019_WithWriteups/forensics/100_thekey_COMPLETE/pics/pyscript.png">

run the script and get the flag! 

<img src="https://raw.githubusercontent.com/Eshaan7/CTFs_datadumps_2019/master/BSidesSF_CTF_2019_WithWriteups/forensics/100_thekey_COMPLETE/pics/runpyscript.png">

oh ok not really, the output is really messed up, what we did was replicate these key-taps on a terminal and that got us a clear idea about the flag, it is a keyboard capture afterall!
But I tried submitting this flag and it kept saying it's wrong so I changed it to uppercase and voila!

The flag is: `CTF{MY_FAVOURITE_EDITOR_IS_VIM}`

#### Thankyou for reading, happy hackin'! ~ [eshaan7](https://eshaan7.cf/)
