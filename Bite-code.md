# Bite-code (Medium)
challenge link: https://ctflearn.com/challenge/368

## Writeup
This rev challenge is about reversing a bytecode of Java program. Using this [wiki page](https://en.wikipedia.org/wiki/List_of_Java_bytecode_instructions) and research
on what are Javas bytecodes and how they work, I understood the program.

Here the program loads the argument to the function, shifts left 3 times and saves in var1.
```
0: iload_0
1: iconst_3
2: ishl
3: istore_1
```

Here it loads the function argument, xors with the number **525024598** and saves in var2.
```
4: iload_0
5: ldc           #2                  // int 525024598
7: ixor
8: istore_2
```

Lastly, the program loads both var1 and var2, xors them, then compares to the number **-889275714**, if they are euqal, returns true else returns false.
```
9: iload_1
10: iload_2
11: ixor
12: ldc           #3                  // int -889275714
14: if_icmpne     21
17: iconst_1
18: goto          22
21: iconst_0
22: ireturn
```

We can write this condition like this `(num << 3) ^ (num ^ 525024598) ==  -889275714` and the flag is the number that will result in **true**.

First thought is to bruteforce it, but having to go trhough 2^32 numbers is a long proccess. The challenge says that there is only one number that will return
true, so perhaps we can calculate it.

What do we know about number? certainly nothing, but I notice something about `(num << 3)` which will lead me to the solution. Noticed? shift left 3 times will result
in the number having 3 0 in the least significant bits! Now do you know what is cool about XOR? if you have `a ^ b = c` then you can take any two numbers from a,b,c 
xor them and get the third number.

We can use that here, for just the 3 least significant bits we know the equation looks like this `000b ^ (num[0:3] ^ 110b) == 110b`. xoring `000b ^ 110b` we will
get the 3 first bits of `(num[0:3] ^ 110b)` which is `000b`, now we can use the same XOR rule I mentioned to find `num[0:3]` like this `num]0:3] = 000b ^ 110b`.
the first 3 bits of the number are `000`! Now we know that `(num << 3)` will result in `000000b` and we do the same proccess. At the end, we will find the number!

It took me a lot of effort to make the code, I couldn't figure out what was wrong with it XD. First I wrote in python, and it was a mistake, couldn't control the size
of the number, and got wrong result. Switched to C and worked like a chram. Complete code looked like this:
```
#include <stdio.h>
int main() {
    int num = 0;
    for(int i = 3; i < 32; i += 3) {
        num = (((num<<3) ^ -889275714) ^ 525024598);
    }
    printf("num: %d\n", num);
    return 0;
}
```
