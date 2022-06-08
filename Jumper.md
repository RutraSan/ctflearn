# Jumper
challenge link: https://ctflearn.com/challenge/369

## Writeup
I didn't read the description carefuly and missed the part where it says *If the user inputs in 'jump'* so I was stuck on this for a long time.

First I notice the `printf` and `fgets` functions (after reading carefuly knowing the input is 'jump'), as well I find the line where the jump is performed
` 80484e2:       ff d0                   call   *%eax <--- What address does this jump to??`

So I know we jump on to the value of `eax`, going backwards first I figure out this code snippet is a loop where it goes 7 iterations of adding 5 so overrall 40
```
 ; loop 8 iterations adding 5 each time
 ; for (int i = 0;i <= 7; i++): count += 5
 80484cc:       8b 45 f0                mov    -0x10(%ebp),%eax
 80484cf:       83 c0 05                add    $0x5,%eax
 80484d2:       89 45 f0                mov    %eax,-0x10(%ebp)
 80484d5:       83 45 f4 01             addl   $0x1,-0xc(%ebp)
 80484d9:       83 7d f4 07             cmpl   $0x7,-0xc(%ebp)
 80484dd:       7e ed                   jle    80484cc <jump+0x3d>
```
 
Next I get this code, which is reading 4 characters from the input to a local variable.
```
 ; read 4 characters from input
 80484a9:       83 c4 10                add    $0x10,%esp
 80484ac:       a1 20 a0 04 08          mov    0x804a020,%eax ; mov eax, 0x804a020
 80484b1:       83 ec 04                sub    $0x4,%esp
 80484b4:       50                      push   %eax
 80484b5:       6a 04                   push   $0x4
 80484b7:       8d 45 f0                lea    -0x10(%ebp),%eax
 80484ba:       50                      push   %eax
 80484bb:       e8 80 fe ff ff          call   8048340 <fgets@plt>
 ```
 
connect everything and we get:
* input 'jump'
* add 40
* jump to the result

So I try both big endian and little endian but it doesn't work and I don't know why, and get stuck. Then I try to layout it in the memory and instinctly add NULL,
but wait now it's 5 characters, so what if the last 'p' isn't even gets inputted, I try again with little endian with the value `0x006d7a6a`, add 40 remove 
the leading zeros and get the flag `0x6d7592`.
