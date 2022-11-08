# cap

> I hid the flag somewhere on the website as a 48 byte hex value, so I know you'll never find it. Just, don't check out how the background is calculated.  
https://hambone.chall.pwnoh.io  
Note: the flag will be in lowercase, not uppercase

BuckeyeCTF 2022 (web, medium, 230 points)

Writeup by danlliu (WolvSec)

## Challenge Files

`dist.py: ASCII text, Python3 source file`

## Solution

If we open up the website, we see a black background with white text saying "This is not the right way".

Let's take a look at `dist.py`:

```python
def get_distances(padded_url : str, flag_path : str):
    distances = []
    for i in range(3):
        # calculate hamming distance on 16 byte subgroups
        flag_subgroup = flag_path[i*32:i*32+32]
                
        z = int(padded_url[i*32:i*32+32], 16)^int(flag_subgroup, 16)
        distances.append(bin(z).count('1'))  
        
    return distances
```

Thus, we see that the flag is 48 bytes long, where the flag path is specified as a hex number. If we go to the URL `https://hambone.chall.pwnoh.io/000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000`, we see that the text color changes to `color:#394b3a`. We can infer that the color is the Hamming distance for each 16 byte subgroup.

Our goal is to find the input that results in Hamming distance 0. We can do this by flipping each bit from left to right, using the Hamming distance oracle to determine which bit is correct (lower Hamming distance). The solve script is shown below:

```python

import requests

URL = 'https://hambone.chall.pwnoh.io/'

bits = [0] * 48 * 8

def bits_to_bytes():
  i = int(''.join(str(b) for b in bits), 2)
  return f'{i:x}'

def get_url():
  t = requests.get(URL + bits_to_bytes()).text
  print(t.split('\n')[13].strip()[17:])
  h0 = t.split('\n')[13].strip()[17:19]
  h1 = t.split('\n')[13].strip()[19:21]
  h2 = t.split('\n')[13].strip()[21:23]
  print(h0, h1, h2)
  return int(h0, 16) + int(h1, 16) + int(h2, 16)

def solve():
  d = get_url()
  for i in range(len(bits)):
    bits[i] = 1
    d2 = get_url()
    if d2 >= d:
      bits[i] = 0
    else:
      d = d2

solve()

print(bits_to_bytes())
```

From here, we allow the script to run, letting the foreground color slowly move towards `#000000`. At this point, the script outputs 
```
000000"> Whoa! How&#39;d you find this? Guess I owe you the flag: buckeye{th3_b4ckgr0und_i5_n0t_4_l13} </p>
00 00 00
ac72c3ecbd95984a48a1890735da8c10b7dd222b9addf2ab7b17778c6b8fc3537852861c969f6738865996481438b29d
```

We have the flag! `buckeye{th3_b4ckgr0und_i5_n0t_4_l13}`.

