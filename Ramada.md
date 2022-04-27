# Ramada  (Easy)
challenge link: https://ctflearn.com/challenge/1009

## Writeup
Another challegne by kcbowhunter which is great, let's see what we have here.

trying to run the program in different ways you might get these output:
* `Usage: Ramada CTFlearn{kernel}`
* `Error: Your flag must start with CTFlearn{`
* `Error: Your flag must end with '}'`
* `Your flag is the wrong length dude!`

Alright so first thing I will do is search for the code that checks the length, most likely `strlen`.

I open gdb and first run `info functions` to see what we have, what interests me is:

`0x00000000000010d0  strlen@plt` for meaning that we it checks flags length using `strlen`.

And this functions that are defined by the program.
```
0x0000000000001100  main
0x0000000000001370  CheckFlag(char const*)
0x00000000000013c0  InitData()
```

First I think that the length is checked in the `CheckFlag` function but disassembling it shows me that I am wrong. Disassembling main we get a big
program, but scrolling through it I find the `strlen` call at `main+151`. shortly later there is the comparison `cmp rax, 0x1f` so I know that the length
of the flag should be 0x1f or 31 in decimal.

Length of "CTFlearn{}" is 10, I make a dummy value of length 21 and put it inside, run the progam and get the following:
```
Your Flag Kernel: aaaaaaaaaaaaaaaaaaaaa
No flag for you!
All Done Ramada!
```

I guess I passed the correct length, let's see what's going on in CheckFlag and InitData functions, but for this I will use Ghidra to make it simpler.

![ghidra1](https://user-images.githubusercontent.com/29147077/165631919-9bce089f-f5ff-4a11-8396-79279ebb0c79.jpg)

InitData function as the name says inits some data. Let's see CheckFlag.

![ghidra2](https://user-images.githubusercontent.com/29147077/165632091-2866710d-7055-4bf9-a37d-aa7cf3f0a6cf.jpg)

This one is not really hard either, in a loop that runs for 0x15 times (`lvar1 != 0x15`) one condition is checked
`if (*(int *)(data + lVar1 * 4) != iVar2 * iVar2 * iVar2)`. From the InitData function we can see that data is a kind of array of ints(4 bytes), and in
this if statement, it checks for value of each one of this data array values for the index in power of 3.

I writed a simple python script to calculate the flag for me, here it is
```
flag = "CTFlearn{"
data = [0x13693,0x6b2c0,0x11a9f9,0x157000,0x1cb91,0x1bb528,0x1bb528,0xded21,0x144f38,0xfb89d,0x169b48,0xd151f,0x8b98b,0x17d140,0xded21,0x1338c0,0x1338c0,0x11a9f9,0x1b000,0x144f38,0x1734eb]
for val in data:
    flag += chr(round(val ** (1/3)))
print(flag + "}")
```

result: CTFlearn{+Lip1zzaner_Stalli0ns}
