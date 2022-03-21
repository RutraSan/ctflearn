# PIN
challenge link: https://ctflearn.com/challenge/379

## Writeup
The program is very simple, it asks for some kind of a pin code, and the correct pin is the flag. So first thing I do is to open `Ghidra` and see what is going
on in this program. I could go straight away into `gdb`, but I am more familiar with Ghidra so I am going to check that first and use it's decompiler to 
get an easier view on the code.

![ghidra1](https://user-images.githubusercontent.com/29147077/159370987-f7de5957-49c0-4b4a-81df-55c01849fd57.jpg)

So yes the main function is very simple. We have `scanf` which gets the input and then a call to a function called `cek`. But it's  decomplied code doesn't help
much. There is literaly just one line code and it's `return (ulong)(param_1 == valid);`.

![ghidra2](https://user-images.githubusercontent.com/29147077/159371471-202a3831-52cf-4b92-8419-2a08ddfc7702.jpg)

The instruction at `0x4005c3` gives away the whole challenge. We see that there is a comparison of the `locl_c` variable, which is the input, and of the `eax`
register. Basically the value can be known at run time, so know I will open GDB and see what is it.

```
gdb rev1
break cek
run
si 2
p $eax
```
done
