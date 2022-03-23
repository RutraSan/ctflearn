# Lost In The Binary  (Hard)
challenge link: https://ctflearn.com/challenge/285

## Writeup
I want to give some attention to the description of the challenge.
>  If you trigger certain anti-debugging techniques, you might get false flags

This hints us that we don't need a debugger, which is great. I runned the program already and it asks for a password, giving a bad one we get:
```
password : 12341234
bad password!
```
Alright let's open Ghidra and see what is going on here. So first thing to notice is that Ghidra couldn't find the `main` function, that's really bad
for those who will try to use a debugger. Likely I read the description so let's just find the function that looks like `main`, since ther is just 5
function anyway.

I quickly find the one with most code and that has prints and scanfs so it's the one. Now I can start reversing it. First thing to catch my eye is 
`ptrace`,I don't know what is it but [Google Helps](https://en.wikipedia.org/wiki/Ptrace) and well basically it's a tool used for debuggers. Alright
let's just continue it doesn't bother us as long as we don't open GDB. I want to find where is the input right? so I search for function that pritns
"password : " and find that there are 2 same print functions, which one we get to?

I continue to analyze the code and find out that after getting the input at those 2 places, there are different if statements.
1. The first is `if (len < 0x11)` which will resume to the encryption
2. The second is `if (0x10 < len` which will print error
Both checks if not passed will print the same error as well, ""the password must be less than 16 character", but the thing is, the second if actually
checks if the input was less then 16 characters, the first one will allow 16. So let's try to enter a 16 long character password, this will tell me where
are we.
```
password : aaaaaaaaaaaaaaaa
bad password!
```

It prints just "bad password!", which means the test was `if (len < 0x11)` and we didn't pass the encryption. Good now I know what I should reverse.

### Fake flags
* If you just need to know how to decrypt the real flag, skip this part. I added it because those were my steps and I decided to include them as well.

![ghidra1](https://user-images.githubusercontent.com/29147077/159686159-bb001e0b-8b7f-4d0d-bec2-e12deef6aec1.jpg)

You can notice that the encryption is far too simple. I will spoiler now, this flag is a false one. So I will quickly just show what the result was.
```
def fake_one():
    key = [-105, -101, -115, -125, -95, -102, -53, -56, -103]
    dec = []

    for v in key:
        dec += chr((~v -1) ^ 2)
    return "".join(dec)
```
This resulted with  **easy_fl4g**. In the program
```
password : easy_f14g
missing arguments
```
The flag was indeed easy, let's move to a different one.


The program said "missing arguments", and we indeed can find this code `if (argc < 5)`. (originaly it was not named 'argc').
If we don't give more then 5 arguments, then we will get into the code for the "easy_fl4g". Let's move out of this if statement and see what other flags
we can get, till we find the real one.

![ghidra2](https://user-images.githubusercontent.com/29147077/159687738-bd57bf14-96f7-467d-8390-69b9e3df5b7c.jpg)

Once again I already changed some of the variable names so it's arleady easier to understand, and what hapeens is that the program checks for  different
matchematical operations with 4 different numeric values passed as the 8,16,24 and 32 argument. I am scared of this and this might as well be a false 
falg, so before decrypting that, let's just make sure there isn't something else.

![ghidra3](https://user-images.githubusercontent.com/29147077/159688417-12fcf13d-93c2-4439-865a-af2929ec3288.jpg)

This is the last flag we can get,and is much easier then the prevoius one. Again I will go over this fast since it will be a fake flag again.
```
def fake_two():
    enc = '7Yq2hrYRn5Y`jga'
    dec = ''
    for c in enc:
        dec += chr(ord(c) ^ 6)
    return dec
```
This the the function to decrypt it, and results with **1_w4nt_Th3_flag** which is incorrect. And the progrma gives this:
```
└─$ ./lost_in_bin 1 2 3 4 5
password : 1_w4nt_Th3_flag
FLAG-7736d3bd5336297bcce7be966668ee84
```
* Notice that I also passed 5 arguments, otherwise it would go to the "easy_fl4g" code.

and the MD5 decrypt give "7Yq2hrYRn5Y\`jga"... that's literally the encrypted "1_w4nt_Th3_flag", what is going on here.
Anyway I understand that we need to go back to the scary code and this one will be the real flag.


### Real flag
Let's analyze what is this scary if statement.
1. 4 different variables
2. 4 different equations
That's a big problem, but since the number of variables matches the number of equations, this is certainly possible to solve with algebra, and I
dont' to to commit a suicide burforcing it since we have **4 64 bit variables** this is **4^(2^64)** different combinations! 
a regular calculator can't display this number.

At first I was getting ready to solve this equation, and decided to eat lunch before it. While eating the lunch, I thought "isn't there already a 
calculator for this?" and [of course there is](https://www.wolframalpha.com/calculators/system-equation-calculator),
so now I don't need to kill myself over this thanks god.

The results are
```
PARAM_32 = 227 
PARAM_8 = 115
PARAM_16 = 317
PARAM_24 = 510
```
Now it's just about passing those as arguments in to the program.
#### Attempt 1
```
─$ ./lost_in_bin 0 0 0 0 0 0 0 0 227 0 0 0 0 0 0 0 115 0 0 0 0 0 0 0 317 0 0 0 0 0 0 0 510
password : asdf
bad password!
```
Why is that worng? let's check the format again, the code is `PARAM_8 = strtol(*(char **)(argv + 8),(char **)0x0,10);` the imortant part right now
is `argv + 8` since we want to put our value in the right place. have placed it at 8, and then I recall that the first argument is always the programs
name, so I make a little change.
#### Attempt 2
```
└─$ ./lost_in_bin 0 0 0 0 0 0 0 227 0 0 0 0 0 0 0 115 0 0 0 0 0 0 0 317 0 0 0 0 0 0 0 510 0
password : asdf
bad password!
```
Still not it, but why? I then check again how `argv` is defined because what I remember is it wasn't `char**`, on top of that, it is actually casted
to it when called to `strol` so maybe that is what I do wrong.
#### Attempt 3
`main(int argc,long argv)` surprise surprise, it's **long**! and long is exactly 8 bytes, this means that each argument takes 8 bytes, so `argv + 8` will
get us to the first argument, in C code you would write `argv + sizeof(long) * 1` and not what Ghidra decompiler did.
```
└─$ ./lost_in_bin 227 115 317 510  
FLAG-21a84f2c7c7fd432edf1686215db05ea
```
And that's the flag, challenge completed huray!
