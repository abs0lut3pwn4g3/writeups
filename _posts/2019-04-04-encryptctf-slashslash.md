---
author: eshaan7
layout: post
---

### Challenge: Slash Slash(for 50 points, static) [WEB]

#### Challenge description: 
	
	//
	file: (handout_slashslash.7z[ ]
	Author: maskofmydisguise

#### Let's capture tis fl4g: 

We are given a `handout_slashslash.7z` file here.
It contains a Python virtual environment and a Flask(python) Application returning JSON data (the flag) at /encryptCTF route.
Now, we know that the Flask Application loads the flag from the environment variable FLAG. This Environment Variable is loaded when the virtual environment is activated using

	source ./env/bin/activate

then when we go to the /encryptCTF route we get this,

```JSON
flag: encryptCTF{}
```

*LMAOOO000000000000OOOOOOOOOO0000*

So, we gotta think now and since the `application.py` file also includes a link to learn about virtual environments and if we concentrate on the challenge name, what dies that tell us? maybe something important is commented out somewhere ? eh, just a thought.

Anyhoo, In the env/bin/activate script you will find the environment variable being exported as an env var (all encoded in base64). Simply decode the base64 to get the flag.

	FLAG: encryptCTF{comments_&_indentations_makes_johnny_a_good_programmer}

We could have also ran
	
	grep -nir encryptCTF

on the app/ and still wouldn't have got anything.
but, if we do

	grep -nir `echo "encryptCTF" | base64`

We get the location of the flag. :)

##### BONUS:

Virtual environments are awesome!
The purpose is to ease development and also store config, requirements.txt, no need to pip install anything.
I made this challenge to introduce people to environment variables, virtual environment and basic web development using Flask microframework which I love. :)

#### Thankyou for reading, happy hackin'! ~ [eshaan7](https://eshaan7.cf/)
