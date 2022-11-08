# bonce

> nonce without a cipher

BuckeyeCTF 2022 (crypto, easy, 142 points)

Writeup by danlliu (WolvSec, first blood!)

## Challenge Files

`bonce.py: ASCII text (Python3 source code)`  
`output.txt: ASCII text`

## Solution

To approach this, we can start by taking a look at the source code in bonce.py:

```python
import random

with open('sample.txt') as file:
    line = file.read()

with open('flag.txt') as file:
    flag = file.read()

samples = [line[i:i+28] for i in range(0, len(line) - 1 - 28, 28)]

samples.insert(random.randint(0, len(samples) - 1), flag)

i = 0
while len(samples) < 40:
    samples.append(samples[len(samples) - i - 2])
    i = random.randint(0, len(samples) - 1)

encrypted = []
for i in range(len(samples)):
    x = samples[i]
    if i < 10:
        nonce = str(i) * 28
    else:
        nonce = str(i) * 14
    encrypted.append(''.join(str(ord(a) ^ ord(b)) + ' ' for a,b in zip(x, nonce)))

with open('output.txt', 'w') as file:
    for i in range(0, 4):
        file.write('input: ' + samples[i] + '\noutput: ' + encrypted[i] + '\n')
    file.write('\n')
    for i in range(4, len(samples)):
        file.write('\ninput: ???\n' + 'output: ' + encrypted[i])
```

Taking a look at this code, we see that at the end of the `for i in range(len(samples))` loop, we have a string XOR taking place between the sample at index `i` and a `nonce` value. In cryptography, a nonce is a single-use value that is used to encrypt a message. Typically, a nonce should be randomly generated and hard to guess. However, this is the opposite of hard to guess. Indeed, we can just generate the value of `nonce` for any value of `i`!

At this point, we can write our solve script. Our general approach is to generate the nonce for each index and XOR it with the corresponding output line. Since XOR is its own inverse, we will be able to get the original input, which will include the flag!

```python

from Crypto.Util.strxor import strxor

def nonce(i):
  if i < 10:
    return str(i) * 28
  else:
    return str(i) * 14

with open('output.txt') as f:
  i = 0
  for line in f:
    if 'output: ' in line:
      numbers = [int(x) for x in line.split()[1:]]
      res = ''.join(chr(a ^ ord(b)) for a,b in zip(numbers, nonce(i)))
      print(res)
      i += 1
```

Running this script gives us a lot of ouptut, which will include the flag!

Flag: `buckeye{some_say_somefish:)}`

Full output:
```
Look in thy glass, and tell 
the face thou viewestNow is 
the time that face should fo
rm another;Whose fresh repai
r if now thou not renewest,T
hou dost beguile the world, 
unbless some mother.For wher
e is she so fair whose unear
â€™d wombDisdains the tillag
e of thy husbandry?Or who is
 he so fond will be the tomb
Of his self-love, to stop po
sterity?Thou art thy motherâ
€™s glass, and she in theeCa
lls back the lovely April of
 her prime:So thou through w
indows of thine age shall se
eDespite of wrinkles this th
y golden time.But if thou li
buckeye{some_say_somefish:)}
ve, rememberâ€™d not to be,D
ie single, and thine image d
ve, rememberâ€™d not to be,D
r if now thou not renewest,T
Look in thy glass, and tell 
sterity?Thou art thy motherâ
unbless some mother.For wher
e is she so fair whose unear
rm another;Whose fresh repai
ve, rememberâ€™d not to be,D
r if now thou not renewest,T
rm another;Whose fresh repai
e of thy husbandry?Or who is
e is she so fair whose unear
e of thy husbandry?Or who is
lls back the lovely April of
e of thy husbandry?Or who is
ve, rememberâ€™d not to be,D
€™s glass, and she in theeCa
rm another;Whose fresh repai
```
