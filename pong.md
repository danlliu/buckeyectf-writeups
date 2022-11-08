# pong

> I dug up my first ever JavaScript game, but this time, my AI is unbeatable!! Hah!  
https://pong.chall.pwnoh.io

BuckeyeCTF 2022 (web, beginner, 60 points)

Writeup by danlliu (WolvSec)

## Solution

The AI is indeed unbeatable :P

When we look at the source code of the website, we see the following script:

```javascript
const socket = io();
const canvas = document.getElementById("game");
/* some variables and functions hidden for clarity */

var s1 = 0;
var s2 = 0;

function reset(ctx) {
    // ...
}

function set() {
    // ...
}

function draw() {
    // ...
}

function tick() {
    // physics

    if(bx < -.1 || bx > 1.1) {
        socket.emit("score", bx);
    }

    draw();
}

function init() {
    draw();
    setInterval(tick, 13);
    // key press handlers ...
}

socket.on("alert", (msg) => alert(msg));
socket.on("begin", (params) => {
    bvx = params.bvx;
    bvy = params.bvy;
});
socket.on("set", (scores) => {
    set();
    s1 = scores.sx1;
    s2 = scores.sx2;
});
```

Here, we see that scoring is handled by `socket.emit("score", bx)`. Thus, we can go to the console, run `socket.emit("score", 1.2)` multiple times, and "score" for ourselves! As we start doing this, we see that the score begins to increase in our favor. Running it until the score bar reaches the middle `alert`s the flag: `buckeye{1f_3v3ry0n3_ch3475_175_f41r}`
