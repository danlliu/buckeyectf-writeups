# scanbook

> Tickets, please.  
https://scanbook.chall.pwnoh.io

BuckeyeCTF 2022 (web, easy, 118 points)

Writeup by danlliu (WolvSec)

## Solution

When we open the website, we are presented with a pastebin-like interface, where we can save text snippets and view them later with a QR code. Let's try creating one!

![scanbook qr1](assets/scanbook-qr.png)

If we run this through a QR code decryptor, such as `pyzbar`, we get the following:

```
>>> from PIL import Image
>>> from pyzbar.pyzbar import decode
>>> decode(Image.open('46112656.png'))
[Decoded(data=b'46112656', type='QRCODE', rect=Rect(left=39, top=39, width=292, height=292), polygon=[Point(x=39, y=39), Point(x=39, y=330), Point(x=331, y=331), Point(x=330, y=39)], quality=1, orientation='UP')]
```

It looks like the QR code stores an identifying number specifying which text snippet is stored. Let's try making one with `0`:

```
>>> import pyqrcode
>>> qr = pyqrcode.create('0')
>>> qr.png('test1.png', scale=6)
```

![scanbook solution](assets/scanbook-sol.png)

From here, we can upload this image to the main page, and we receive a text fragment with the flag: `buckeye{4n_1d_numb3r_15_N07_4_p455w0rd}`
