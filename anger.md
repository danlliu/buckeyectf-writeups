# anger

> Fight anger with angr.

BuckeyeCTF 2022 (rev, easy, 216 points)

Writeup by danlliu (WolvSec, first blood!)

## Challenge Files

`angry: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=722f16df6b8b09063ca2c42ce04d8d1930eba1c8, for GNU/Linux 3.2.0, stripped`

## Solution

Here, we have a simple executable reversing challenge, where our goal is to find the correct input to the file, which will be the flag. To begin this challenge, we can start by decompiling the executable in Ghidra. With some renamed variables and functions, we see the following code:

```c
uint rotl(byte param_1,byte param_2)
{
  return (int)(uint)param_1 >> (8 - param_2 % 8 & 0x1f) | (uint)param_1 << param_2 % 8;
}

void mutateString(char *str,char seed)
{
  char rotated;
  size_t sVar1;
  char rotamount;
  int i;
  
  sVar1 = strlen(str);
  rotamount = seed;
  for (i = 0; (ulong)(long)i < sVar1; i = i + 1) {
    rotated = rotl(str[i],rotamount);
    str[i] = rotated;
    rotamount = str[i];
  }
  return;
}

int main(void)
{
  int iVar1;
  size_t len;
  size_t sVar2;
  long in_FS_OFFSET;
  char str [104];
  long local_20;
  
  local_20 = *(long *)(in_FS_OFFSET + 0x28);
  puts("Welcome to Angry Frob, not your normal frob.");
  printf("The special number today is... %d\n",0x2a);
  puts("Enter something for us to frobnicate: ");
  fgets(str,99,stdin);
  len = strcspn(str,"\n");
  str[len] = '\0';
  mutateString(str,0x2a);
  len = strlen(str);
  sVar2 = strlen(&DAT_00104020);
  if (len == sVar2) {
    iVar1 = strcmp(str,&DAT_00104020);
    if (iVar1 == 0) {
      puts("Congratulations, you found the special string to frob.");
      goto LAB_001013d4;
    }
  }
  puts("Failure, you didn\'t send an interesting string.");
LAB_001013d4:
  if (local_20 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}
```

At a quick glance, we see three important functions, which I have called `rotl`, `mutateString`, and `main`. Let's go through what each of these functions do.

`rotl` is a simple left bit rotation. While it returns `uint`, the result used in `mutateString` is placed into a `char`, so the eventual result is trimmed down to 8 bits.

`mutateString` is the key part of this executable which must be reversed. `mutateString` takes in a string, as well as a "seed" value. Starting with the first character of the string, it calls `rotl` on the character at that position and `seed`, then updates `seed` to be the value of the character after rotation. Thus, the second character is rotated left by the value of the mutated first character.

Finally, `main` calls `mutateString` on the input wiht the value `0x2a`. After this, it compares the mutated string with `DAT_00104020`. In Ghidra, we can extract the value of the string at `DAT_00104020`:

```
                             DAT_00104020                                    XREF[2]:     main:00101392(*), 
                                                                                          main:001013a7(*)  
        00104020 89              ??         89h
        00104021 ea              ??         EAh
        00104022 8d              ??         8Dh
        00104023 6d              ??         6Dh    m
        00104024 ac              ??         ACh
        00104025 97              ??         97h
        00104026 b2              ??         B2h
        00104027 ed              ??         EDh
        00104028 6e              ??         6Eh    n
        00104029 1d              ??         1Dh
        0010402a 24              ??         24h    $
        0010402b c6              ??         C6h
        0010402c 1b              ??         1Bh
        0010402d fa              ??         FAh
        0010402e 89              ??         89h
        0010402f 66              ??         66h    f
        00104030 1d              ??         1Dh
        00104031 8e              ??         8Eh
        00104032 cc              ??         CCh
        00104033 27              ??         27h    '
        00104034 af              ??         AFh
        00104035 3a              ??         3Ah    :
        00104036 a1              ??         A1h
        00104037 68              ??         68h    h
        00104038 6e              ??         6Eh    n
        00104039 d7              ??         D7h
        0010403a b9              ??         B9h
        0010403b e8              ??         E8h
        0010403c 72              ??         72h    r
        0010403d 99              ??         99h
        0010403e e4              ??         E4h
        0010403f 97              ??         97h
        00104040 be              ??         BEh
        00104041 00              ??         00h
```

At this point, we have all the information we need! Our approach will be to go through each character in this bytestring. First, we save the mutated value (from `DAT_00104020`) for the next character. Then, we rotate _right_ by the value of `seed` at that point (`0x2a` at the beginning or the value of the previous character in `DAT_00104020`), and print out the value of this character. The final solve script is shown below:

```python

correct = bytes.fromhex('89ea8d6dac97b2ed6e1d24c61bfa89661d8ecc27af3aa1686ed7b9e87299e497be00')

def rotr(c, x):
    return ((c >> (x % 8)) | (c << (8 - (x % 8)))) % 128

seed = 0x2a
for c in correct:
    new_amount = c
    old_char = rotr(c, seed)
    seed = new_amount
    print(chr(old_char), end='')
```

Running this gives us the flag!

```
> buckeye{st!ll_b3tt3r_th4n_strfry}
```
