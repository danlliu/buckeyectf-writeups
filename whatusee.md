# textual

> I definitely made a Git repo, but I somehow broke it. Something about not getting a HEAD of myself.

BuckeyeCTF 2022 (misc, beginner, 75 points)

Writeup by danlliu (WolvSec)

## Challenge Files

In this challenge, our goal is to find the flag in the Git history. However, if we open up `flag.txt`, we don't really see anything useful.

To look at the history of a Git repo, we can look at `.git/logs/HEAD`:

```
0000000000000000000000000000000000000000 30b26c2c7e8d48612cc5f6da4a374e262ccf860c NOT Gent Semaj  <jim@bo.hacked> 1667608995 -0400	commit (initial): Initial commit
30b26c2c7e8d48612cc5f6da4a374e262ccf860c 02417f390d6d72ad68082cd243760461aa3bd42a Shannon's Man <peanut@butter.jellytime> 1667609597 -0400	commit: Added Andy Warhol effect to file

7ae8453a76a41d40bdfcc7992175390f70ba9fdf c4681c8d561653cee9ecbea5d5ca5629adfd67a4 Matthew Ayers <matt@matthewayers.com> 1667704926 -0400	commit: Hid the flag
```

We see that the first two commits are linked to each other, but the last commit ("Hid the flag") starts at `7ae8453a76a41d40bdfcc7992175390f70ba9fdf`, which must be the commit with the flag. We can use `git cat-file -p` to analyze this specific point:

```
$ git cat-file -p 7ae8453a76a41d40bdfcc7992175390f70ba9fdf
tree fe6f925cc960a35e5615d5988004ca9b6345469f
parent 02417f390d6d72ad68082cd243760461aa3bd42a
author Matthew Ayers <matt@matthewayers.com> 1667610223 -0400
committer Matthew Ayers <matt@matthewayers.com> 1667610223 -0400

Added more stuff

$ git cat-file -p fe6f925cc960a35e5615d5988004ca9b6345469f
100644 blob 7ca1eaa16d3c318b3621d1e70c326c098f71ad2d    flag

$ git cat-file -p 7ca1eaa16d3c318b3621d1e70c326c098f71ad2d
buckeye{G1t_w@S_N@m3D_afT3r_Torvalds}
buckeye{placeholder_flag}
buckeye{placeholder_flag}
buckeye{placeholder_flag}
buckeye{placeholder_flag}
...
```

There's the flag!


