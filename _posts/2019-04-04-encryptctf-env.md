---
author: eshaan7
layout: post
---

### Challenge: ENV(for 100 points, static) [WEB]

#### Challenge description: 
	
	Einstien said,"_time_ was relative, right?"
	link: 
	Author: maskofmydisguise

#### Let's capture tis fl4g: 

	curl http://104.154.106.182:6060/whatsthetime/$(date "+%s")

Repeat this until it throws you the flag.

Can also be done with python,

```python
import requests
import time


host = "104.154.106.182"
port = '6060'

while True:
    response = requests.get('http://104.154.106.182:6060/whatsthetime/'+str(int(time.time())))
    if "404" not in response.content:
        print response.content
        break
```

Repeat this until it throws you the flag.

	FLAG: encryptCTF{v1rtualenvs_4re_c00l}



