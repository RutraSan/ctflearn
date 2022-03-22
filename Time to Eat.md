# Time to Eat (Medium)
challenge link: https://ctflearn.com/challenge/743

## Writeup
we are given a python program that is confusing with it's syntax, and we need to understand what the programmer tried to do, and then find the 
string that passes their check, which will give us the flag.

So first we have some definitions at the top of the program, using find/replace we chagne all that. The functions are still confusing but we
keep going and learn couple of things from the "Eat" (main) funciton. Lenght of the secret string should be 9, there are 3 digit number at begging and
at the end. I see the `EAt` function which is the one encrypting the input, I reverse that and the other functions.

Basically, the EAt function gets 2 string:
1. The first number multiplayed by 3 + **the input reversed**
2. "Eat" + len of the input + the last number reversed.

Later it creates one string which is the combination of both. Every `index % 3 == 0` character is from the second string, others are from the first.
In the main function we know what the encrypted string should look like, I take it and make a decryption function. It finds the 2 original strings, then
takes the first one, takes last 9 characters, reverses them, and that's the string we need to input.

```
def dec():
    key = "E10a23t9090t9ae0140"
    counter = 0
    first_and_rev = ""# 1023900tae14
    eat_len_last = ""
    for i in range(len(key)):
        if i % 3 == 0:
            eat_len_last += key[i]
        else:
            first_and_rev += key[i]
    
    print(first_and_rev)
    print(eat_len_last)
    return rev(first_and_rev[len(first_and_rev) - 9::])
```
    
This produces `41eat0093` which is srong, the '3' should be at the beggining and I figured it out myself, the script did a good enough job i dunno
why the 3 appears wrong.
