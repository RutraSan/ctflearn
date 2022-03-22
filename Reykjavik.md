# Reykjavik (Easy)
challenge link: https://ctflearn.com/challenge/990

## Writeup
This is a very begginer friendly challenge, the author even provides links to differen learning videos and websites which is very cool,
I will try it stargiht away though since I have some experience with reveresing afterall.

First time running the program will show the usage of it. I run it the way shown and get the greeting message with the message
`Sorry Dude, 'CTFlearn{flag}' is not the flag :-(`
Well, what a shame. Let's open Ghidra and find the actual flag.

Opening the main function you right away see `CTFlearn{Is_This_A_False_Flag?}` which is as stated, a false flag. But later on I find the print message
for entering the real flag, so let's reverse the code to get there.

![ghidra1](https://user-images.githubusercontent.com/29147077/159374563-87c766fe-e91c-4de3-ba0e-c2ffc6c7507f.jpg)

I want to take your attention to the prints. on the Congratulations message, the program passes `&local_38`, and on the error message he prints `__s2`

![ghidra2](https://user-images.githubusercontent.com/29147077/159434356-a42e237d-3fd1-475d-a335-c376e14cfe45.jpg)

This gives me a little information on what is what. `__s2` is the string we pass to main, and `local_38` is the flag, so I will rename that real quick.

![Ghidra4](https://user-images.githubusercontent.com/29147077/159436165-412ccc3b-065f-4aee-9a4e-3b3f7ddf5485.jpg)

Now you can see here the variables and it's a bit cleaner. The string we compare our input to is `ulong`, long is 8 bytes. and we have more variables
declared later, and those most likely are part of the string as well, but we dont' know yet which of them are.

Let's go back to the decompiled code (img1), look at the code before the print of the flag, those operations are really simple,just XORing all the bytes
of `data` with the number `0xab`. But checking what `data` is will show me it's undefined. Just to check myself I also look at the assembly code.

![ghidra3](https://user-images.githubusercontent.com/29147077/159437133-e66b4b20-9ca4-4eb9-9305-943b4eb1d73d.jpg)

Indeed we see the `RAX` is assigned with `0xABABABABABABABAB`, and later he is used in XOR operations. If we check the code, the XOR happens between `AX`
and a value located at `data`. It's fun to see actually that the pointer to different location at `data` are advanced by 8, since `ulong` is 8 bytes long.
This just confirms me that I understood the program, now it's time to open GDB and check in run time what is located in `data`, then xor it with `0xab` *
number of bytes.

So first thing I do is settin a breakpoint before the XOR operations.
` breakpoint 0x55555555510a`
now with the breakpoint set, I run the program and the breakpoint stops me, and now I try to print `data`s value.

```
p $data
x/ data
```

This two commands don't return me what I want. the print says it's void and examine command can't access the memory location, which is 0x0? Now that 
seems not right. I check the assembly and GDB has a comment next to the instruction which get the address to data, `# 0x555555558010 <data> `. Now this 
might be helpful, GDB can't access `data` but it sure can access a given address.

![ghidra5](https://user-images.githubusercontent.com/29147077/159466810-19cc0fa6-6f9d-48cb-85db-284e9240ddec.jpg)

Now this looks right. I printed the whole string and now I can write a python script to calculte for me the result of XORing each number with 0xab, 
and convert to string. I didn't get anything right, and now I see that the number are too big for 1 byte which is not what supposed to be. This time I
print the unsigned decimal values of this memory location, and I guess the length should be 27 because that's are all the values the program uses.

```
for num in s.split():
    dec += chr(int(num) ^ 0xab)
```

I run the script (s is the data, and dec is an empty string), and get **CTFlearn{Eye_L0ve_Iceland_U**,
this isn't defenetly not a complete flag, but we are on the way. I decide to go check the stack in GDB, I could do that before but didn't think of
it for some reason. I run till the strcmp function and run `x/s $rsp` which shows the exact same thing, and yet for some reason the program says it's
wrong, first thought is that the it doens't compare to our whole string, but just a part of it, but that wouldn't make much sense. I try to check
more data in GDB, maybe there are some bytes I missed but no, it's 0 after 'U' and before the string. I decide to run the compare function and see
what it returns, maybe it will be a hint. It returns 40 but I don't really know what to do with that. Just to make sure, I once again print the values on
the stack, and I get **CTFlearn{Eye_L0ve_Iceland_}**, I instantly try it and it works lmao.

So what was the problem? I printed the stack value one instruction earlier, beore the assignment of '}' at the end, because I thought that the higlighted
line is the one that was executed rn and not the upcoming. Lesson learnd

