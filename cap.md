# cap

> Someone got rid of my macros and now my program won't compile fr fr

BuckeyeCTF 2022 (rev, easy, 101 points)

Writeup by danlliu (WolvSec, first blood!)

## Challenge Files

`cap.c: c program text, ASCII text`

## Solution

We are given the following C program:

```c
legit brutus ongod clean mf x af
finna
    clean val lookin cap fr
    poppin ongod lit i lookin cap fr i lowkey 11 fr i playin af
    finna
        val lookin val dub 5 fr
    tho
    mf x lookin val fr fr
    boutta ongod val lowkey 104 af
        val playin fr
    mf ongod x dub bussin af lookin val fr fr
    val lookin val wack 2 fr
    mf ongod x dub 2 af lookin val fr
    mf ongod x dub 3 af lookin val dub 3 fr
    lit two lookin 2 fr
    val lookin val lackin two mf ongod 3 dub two lackin 4 af dub 3 fr
    mf ongod x dub 5 af lookin val fr
    lit six lookin 6 fr
    val lookin val mf two lackin two fr
    mf ongod x dub 6 af lookin val fr fr
    val lookin ongod val lackin six af wack two fr
    mf ongod x dub 7 af lookin val fr
    poppin ongod lit i lookin cap fr i lowkey six; i playin af
        val playin fr
    mf ongod x dub 8 af lookin val fr
tho

legit kinda ongod clean mf y af
finna
    clean val lookin 109 fr
    poppin ongod lit i lookin cap fr i lowkey 9 fr i playin af
    finna
        tryna ongod i be 2 af
            chill fr
        tryna ongod i be 8 af finna
            y yeet ongod bussin dub bussin af lackin ongod 2 wack 2 af mf 2 rn lookin val wack ongod bussin dub bussin af lackin 6;
        tho
        tryna ongod i be 4 af finna
            lit ten lookin 10 fr
            val lookin val dub ongod bussin dub bussin af mf ten lackin ongod bussin lackin cap af fr
            mf y lookin val downbad fr
            lit j lookin 10 fr fr
            y playin fr
            respectfully finna
                val downbad fr
                j downbad fr
            tho boutta ongod j highkey cap af fr
        tho
        tryna ongod i be cap af finna
            mf y lookin val fr
            lit j lookin bussin lackin bussin fr
            boutta ongod j lowkey 7 af finna
                val downbad fr
                j lookin j dub bussin fr
            tho
            y playin fr fr
        tho
        tryna ongod i be 5 like i be 6 af finna
            val lookin val wack 2 fr
            mf y lookin val fr
            y lookin y dub bussin fr
            val lookin val mf 2 fr fr
        tho
        tryna ongod i be 3 af finna
            lit a lookin y yeet lackin bussin rn fr
            val lookin a dub bussin dub bussin dub bussin fr
            mf y lookin val fr
            y playin fr
        tho
        tryna ongod i be 7 af finna
            y playin fr
            poppin ongod lit j lookin 4 fr j highkey cap fr j downbad af finna
                val lookin val dub j wack j fr
            tho
            y yeet cap rn lookin val fr
            y downbad fr
        tho
        tryna ongod i be bussin af finna
            boutta ongod cap af finna
                val lookin val mf ongod bussin dub bussin af fr fr
                sheeeesh ongod "you thought\n" af fr
            tho
            mf y lookin val playin fr
            y lookin y dub 2 fr
        tho
    tho
tho

legit wilin ongod clean mf z bruh lit n af
finna
    tryna ongod no n af
        deadass fr
    lit val lookin mf ongod z lackin bussin af fr fr
    mf z lookin ongod n be 4 af sus val mf 2 lackin 1
        drip ongod n be 2 af sus ongod val dub 5 af wack 2
        drip ongod n be 6 af sus val dub 15
        drip ongod n be bussin af sus val mf 2 dub 8
        drip ongod n be 3 af sus val dub 4
        drip val wack 2 lackin 7 fr
    wilin ongod playin z bruh downbad n af fr fr
tho

lit main ongod af
finna
    clean flag yeet rn lookin "buckeye{__________________________}" fr
    brutus ongod flag dub 8 af fr
    kinda ongod flag dub 18 af fr fr
    wilin ongod flag dub 28 bruh 6 af fr

    sheeeesh ongod "%s\n" bruh flag af fr
    deadass cap fr
tho
```

Oh boy. Even my friends text more readable than this.

This challenge is likely inspired by a series of internet memes regarding (ab)use of `#define` in C to make C code look like... well... anything (see [this r/programmerhumor post](https://www.reddit.com/r/ProgrammerHumor/comments/wnixa5/this_is_so_cursed/) for an example)

Ok, it's time for some logical deduction. Hope you're ready.

`fr` appears at the end of each line. This is C, so `#define fr ;`.

`finna` and `tho` appear on their own lines, without `fr` after them. Additionally, they bound indented blocks. Thus, `#define finna {` and `#define tho }`.

`ongod` and `af` appear after `main` and before `finna / {`, which means that they must be `(` and `)`, respectively.

From here, we can start to see that we have four functions (`brutus`, `kinda`, `wilin`, and `main`). `legit` and `lit` are the return types of these functions. We will work out these return types later.

Next, let's take a look at `clean flag yeet rn lookin "buckeye{__________________________}" fr`. We can reasonably guess that we're defining a string variable called `flag` that is equal to "buckeye{__________________________}"`. There are three possible syntaxes for this in C:

```c
char* flag = "buckeye{__________________________}";
char flag[] = "buckeye{__________________________}";
char flag[36] = "buckeye{__________________________}";
```

In this case, we can guess that each word represents a single unique token in the C code. Thus, `char flag[]...` makes the most sense:

- `#define clean char`
- `#define yeet [`
- `#define rn ]`
- `#define lookin =`

Later, in `main`, we see:

```c
sheeeesh ongod "%s\n" bruh flag af fr
```

The presence of `"%s\n"` immediately makes this seem like `printf`. We can guess that this program prints the flag, meaning that this is likely:

```c
printf ( "%s\n" , flag ) ;
```

Thus, `#define sheeeesh printf` and `#define bruh ,`.

Finally, at the end of `main`, we have

```c
deadass cap fr
```

Considering `cap` refers to a false statement in slang, and `false` is equivalent to `0` in C, we can guess that this refers to `return 0 ;`. `#define deadass return`, `#define cap 0`.

Next, let's take a look at the function signature of, for example, `brutus`:

```c
legit brutus ongod clean mf x af
```

What we know so far gives us:

```c
<legit> brutus ( char <mf> x )
```

Additionally, in `main`, we have:

```c
brutus ongod flag dub 8 af fr
```

which translates to

```c
brutus ( flag <dub> 8 ) ;
```

We see here that we're passing in some modification of `flag`, meaning that likely, we are passing in `char *` to `brutus`. The other option is `char unsigned`, but this is very unlikely considering that a string is being passed in in some form. Thus, `#define mf *`.

Additionally, we do not see any `deadass / return` statements in these functions. Thus, we can guess `#define legit void`.

Now, we have to look at the body of `brutus`:

```c
    clean val = 0 ;
    poppin ( lit i = 0 ; i lowkey 11 ; i playin )
    {
        val = val dub 5 ;
    }
    * x = val ; ;
    boutta ( val lowkey 104 )
        val playin ;
    * ( x dub bussin ) = val ; ;
    val = val wack 2 ;
    * ( x dub 2 ) = val ;
    * ( x dub 3 ) = val dub 3 ;
    lit two = 2 ;
    val = val lackin two * ( 3 dub two lackin 4 ) dub 3 ;
    * ( x dub 5 ) = val ;
    lit six = 6 ;
    val = val * two lackin two ;
    * ( x dub 6 ) = val ; ;
    val = ( val lackin six ) wack two ;
    * ( x dub 7 ) = val ;
    poppin ( lit i = 0 ; i lowkey six; i playin )
        val playin ;
    * ( x dub 8 ) = val ;
```

First, let's take a look at:

```c
    poppin ( lit i = 0 ; i lowkey 11 ; i playin )
    {
        val = val dub 5 ;
    }
```

There is only one syntax that fits this pattern: a for loop.

```c
    for ( int i = 0 ; i < 11 ; i ++ )
    {
        val = val dub 5 ;
    }
```

Thus, `#define poppin for`, `#define lit int`, `#define lowkey <`, `#define playin ++`.

We can make this substitution in the remainder of the code:

```c
    boutta ( val < 104 )
        val ++ ;
```

The two syntaxes that fit here are `while` and `if`. Let's come back to this later.

For now, we move on to the body of `kinda`:

```c
    char val = 109 ;
    for ( int i = 0 ; i < 9 ; i ++ )
    {
        tryna ( i be 2 )
            chill ;
        tryna ( i be 8 ) {
            y [ ( bussin dub bussin ) lackin ( 2 wack 2 ) * 2 ] = val wack ( bussin dub bussin ) lackin 6;
        }
        tryna ( i be 4 ) {
            int ten = 10 ;
            val = val dub ( bussin dub bussin ) * ten lackin ( bussin lackin 0 ) ;
            * y = val downbad ;
            int j = 10 ; ;
            y ++ ;
            respectfully {
                val downbad ;
                j downbad ;
            } boutta ( j highkey 0 ) ;
        }
        tryna ( i be 0 ) {
            * y = val ;
            int j = bussin lackin bussin ;
            boutta ( j < 7 ) {
                val downbad ;
                j = j dub bussin ;
            }
            y ++ ; ;
        }
        tryna ( i be 5 like i be 6 ) {
            val = val wack 2 ;
            * y = val ;
            y = y dub bussin ;
            val = val * 2 ; ;
        }
        tryna ( i be 3 ) {
            int a = y yeet lackin bussin rn ;
            val = a dub bussin dub bussin dub bussin ;
            * y = val ;
            y ++ ;
        }
        tryna ( i be 7 ) {
            y ++ ;
            for ( int j = 4 ; j highkey 0 ; j downbad ) {
                val = val dub j wack j ;
            }
            y yeet 0 rn = val ;
            y downbad ;
        }
        tryna ( i be bussin ) {
            boutta ( 0 ) {
                val = val * ( bussin dub bussin ) ; ;
                printf ( "you thought\n" ) ;
            }
            * y = val ++ ;
            y = y dub 2 ;
        }
    }
```

Here, we can start with the obvious syntax.

```c
            respectfully {
                val downbad ;
                j downbad ;
            } boutta ( j highkey 0 ) ;
```

is a `do/while` loop, thus `#define respectfully do` and `#define boutta while`.

We know that `boutta` is `while`, meaning that `tryna` must be `if`. Thus, we see that we have a conditional chain that includes all the values between 0 and 8. We can guess `#define be ==`. The only value missing in the conditional is `1`, which must be `bussin`.

Next, let's take a look at the for loop:

```c
            for ( int j = 4 ; j highkey 0 ; j downbad ) {
                val = val dub j wack j ;
            }
```

We can guess `#define highkey >` and `#define downbad --`.

In the conditionals, we see:

```c
        tryna ( i == 5 like i == 6 ) {
            val = val wack 2 ;
            * y = val ;
            y = y dub 1 ;
            val = val * 2 ; ;
        }
```

Thus, `#define like ||`.

Finally, we can take a look at the following line in the `if (i == 8)` condition:

```c
            y [ ( 1 dub 1 ) lackin ( 2 wack 2 ) * 2 ] = val wack ( 1 dub 1 ) lackin 6;
```

Combined with the fact that we pass in `flag dub 8`, `flag dub 18`, and `flag dub 28` into the three functions in `main`, we can guess that `#define dub +`.

```c
            y [ ( 1 + 1 ) lackin ( 2 wack 2 ) * 2 ] = val wack ( 1 + 1 ) lackin 6;
```

We can guess `#define lackin -` based on its name. Thus, `wack` is the remaining operator, which is `/`.

At this point, the code is becoming more readable

```c
void brutus ( char * x )
{
    char val = 0 ;
    for ( int i = 0 ; i < 11 ; i ++ )
    {
        val = val + 5 ;
    }
    * x = val ; ;
    while ( val < 104 )
        val ++ ;
    * ( x + 1 ) = val ; ;
    val = val / 2 ;
    * ( x + 2 ) = val ;
    * ( x + 3 ) = val + 3 ;
    int two = 2 ;
    val = val - two * ( 3 + two - 4 ) + 3 ;
    * ( x + 5 ) = val ;
    int six = 6 ;
    val = val * two - two ;
    * ( x + 6 ) = val ; ;
    val = ( val - six ) / two ;
    * ( x + 7 ) = val ;
    for ( int i = 0 ; i < six; i ++ )
        val ++ ;
    * ( x + 8 ) = val ;
}

void kinda ( char * y )
{
    char val = 109 ;
    for ( int i = 0 ; i < 9 ; i ++ )
    {
        if ( i == 2 )
            chill ;
        if ( i == 8 ) {
            y [ ( 1 + 1 ) - ( 2 / 2 ) * 2 ] = val / ( 1 + 1 ) - 6;
        }
        if ( i == 4 ) {
            int ten = 10 ;
            val = val + ( 1 + 1 ) * ten - ( 1 - 0 ) ;
            * y = val -- ;
            int j = 10 ; ;
            y ++ ;
            do {
                val -- ;
                j -- ;
            } while ( j > 0 ) ;
        }
        if ( i == 0 ) {
            * y = val ;
            int j = 1 - 1 ;
            while ( j < 7 ) {
                val -- ;
                j = j + 1 ;
            }
            y ++ ; ;
        }
        if ( i == 5 || i == 6 ) {
            val = val / 2 ;
            * y = val ;
            y = y + 1 ;
            val = val * 2 ; ;
        }
        if ( i == 3 ) {
            int a = y [ - 1 ] ;
            val = a + 1 + 1 + 1 ;
            * y = val ;
            y ++ ;
        }
        if ( i == 7 ) {
            y ++ ;
            for ( int j = 4 ; j > 0 ; j -- ) {
                val = val + j / j ;
            }
            y [ 0 ] = val ;
            y -- ;
        }
        if ( i == 1 ) {
            while ( 0 ) {
                val = val * ( 1 + 1 ) ; ;
                printf ( "you thought\n" ) ;
            }
            * y = val ++ ;
            y = y + 2 ;
        }
    }
}

void wilin ( char * z , int n )
{
    if ( no n )
        return ;
    int val = * ( z - 1 ) ; ;
    * z = ( n == 4 ) sus val * 2 - 1
        drip ( n == 2 ) sus ( val + 5 ) / 2
        drip ( n == 6 ) sus val + 15
        drip ( n == 1 ) sus val * 2 + 8
        drip ( n == 3 ) sus val + 4
        drip val / 2 - 7 ;
    wilin ( ++ z , -- n ) ; ;
}

int main ( )
{
    char flag [ ] = "buckeye{__________________________}" ;
    brutus ( flag + 8 ) ;
    kinda ( flag + 18 ) ; ;
    wilin ( flag + 28 , 6 ) ;

    printf ( "%s\n" , flag ) ;
    return 0 ;
}
```

At this point, there's only a few things left.

`#define chill continue`. This is the only statement that makes sense in the body of `kinda` that does not make the other conditionals unreachable.

`#define sus ?` and `#define drip :`. The body of `wilin` uses a chained ternary statement.

`#define no !`: we can guess this by meaning, as well as by guessing that `if ( no n )` is acting as a check if `n` is not 0.

From here, we can compile `cap.c` with these `#defines`. Shown below is the updated `cap.c`, including all macros:

```c
#include <stdlib.h>
#include <stdio.h>

#define cap 0
#define lit int
#define bussin 1
#define no !
#define sus ?
#define fr ;
#define legit void
#define finna {
#define be ==
#define boutta while
#define bruh ,
#define deadass return
#define yikes ???
#define ongod (
#define clean char
#define yeet [
#define mf *
#define tryna if
#define tho }
#define respectfully do
#define like ||
#define lackin -
#define poppin for
#define drip :
#define rn ]
#define chill continue
#define af )
#define lowkey <
#define sheeeesh printf
#define lookin =
#define downbad --
#define playin ++
#define wack /
#define dub +
#define highkey >

legit brutus ongod clean mf x af
finna
    clean val lookin cap fr
    poppin ongod lit i lookin cap fr i lowkey 11 fr i playin af
    finna
        val lookin val dub 5 fr
    tho
    mf x lookin val fr fr
    boutta ongod val lowkey 104 af
        val playin fr
    mf ongod x dub bussin af lookin val fr fr
    val lookin val wack 2 fr
    mf ongod x dub 2 af lookin val fr
    mf ongod x dub 3 af lookin val dub 3 fr
    lit two lookin 2 fr
    val lookin val lackin two mf ongod 3 dub two lackin 4 af dub 3 fr
    mf ongod x dub 5 af lookin val fr
    lit six lookin 6 fr
    val lookin val mf two lackin two fr
    mf ongod x dub 6 af lookin val fr fr
    val lookin ongod val lackin six af wack two fr
    mf ongod x dub 7 af lookin val fr
    poppin ongod lit i lookin cap fr i lowkey six; i playin af
        val playin fr
    mf ongod x dub 8 af lookin val fr
tho

legit kinda ongod clean mf y af
finna
    clean val lookin 109 fr
    poppin ongod lit i lookin cap fr i lowkey 9 fr i playin af
    finna
        tryna ongod i be 2 af
            chill fr
        tryna ongod i be 8 af finna
            y yeet ongod bussin dub bussin af lackin ongod 2 wack 2 af mf 2 rn lookin val wack ongod bussin dub bussin af lackin 6;
        tho
        tryna ongod i be 4 af finna
            lit ten lookin 10 fr
            val lookin val dub ongod bussin dub bussin af mf ten lackin ongod bussin lackin cap af fr
            mf y lookin val downbad fr
            lit j lookin 10 fr fr
            y playin fr
            respectfully finna
                val downbad fr
                j downbad fr
            tho boutta ongod j highkey cap af fr
        tho
        tryna ongod i be cap af finna
            mf y lookin val fr
            lit j lookin bussin lackin bussin fr
            boutta ongod j lowkey 7 af finna
                val downbad fr
                j lookin j dub bussin fr
            tho
            y playin fr fr
        tho
        tryna ongod i be 5 like i be 6 af finna
            val lookin val wack 2 fr
            mf y lookin val fr
            y lookin y dub bussin fr
            val lookin val mf 2 fr fr
        tho
        tryna ongod i be 3 af finna
            lit a lookin y yeet lackin bussin rn fr
            val lookin a dub bussin dub bussin dub bussin fr
            mf y lookin val fr
            y playin fr
        tho
        tryna ongod i be 7 af finna
            y playin fr
            poppin ongod lit j lookin 4 fr j highkey cap fr j downbad af finna
                val lookin val dub j wack j fr
            tho
            y yeet cap rn lookin val fr
            y downbad fr
        tho
        tryna ongod i be bussin af finna
            boutta ongod cap af finna
                val lookin val mf ongod bussin dub bussin af fr fr
                sheeeesh ongod "you thought\n" af fr
            tho
            mf y lookin val playin fr
            y lookin y dub 2 fr
        tho
    tho
tho

legit wilin ongod clean mf z bruh lit n af
finna
    tryna ongod no n af
        deadass fr
    lit val lookin mf ongod z lackin bussin af fr fr
    mf z lookin ongod n be 4 af sus val mf 2 lackin 1
        drip ongod n be 2 af sus ongod val dub 5 af wack 2
        drip ongod n be 6 af sus val dub 15
        drip ongod n be bussin af sus val mf 2 dub 8
        drip ongod n be 3 af sus val dub 4
        drip val wack 2 lackin 7 fr
    wilin ongod playin z bruh downbad n af fr fr
tho

lit main ongod af
finna
    clean flag yeet rn lookin "buckeye{__________________________}" fr
    brutus ongod flag dub 8 af fr
    kinda ongod flag dub 18 af fr fr
    wilin ongod flag dub 28 bruh 6 af fr

    sheeeesh ongod "%s\n" bruh flag af fr
    deadass cap fr
tho
```

Compiling and running gives us the flag!

`buckeye{7h47_5h17_mf_bu551n_n0_c4p}`
