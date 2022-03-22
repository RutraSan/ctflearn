# Reverse Me (Meduim)
challenge link: https://ctflearn.com/challenge/989

## Writeup
The program asks for the flag as input...

Well, I open Ghidra and let's figure out the flag. In the main function there is a declaration of an array, 26 different random values. Most likey it's 
the encrypted flag, and our task is to decrypt it. Later indeed two intersting function are used `encrypt` and `shuffle`, then, it compares the 
encryped flag with the result of this functions. Alright let's see the decryption.

![ghidra1](https://user-images.githubusercontent.com/29147077/159574211-d4e2f5eb-315a-4099-82d8-bd375a16b773.jpg)

now that looks scary, we have a declarion of an array which is the key, and the encryption is basically xoring each byte of the given string (the flag)
with value from the key, the problem is to understand those lines.

```
  while( true ) {
    sVar1 = strlen(__block);
    if (sVar1 <= (ulong)(long)counter) break;
    uVar3 = (uint)(counter >> 0x1f) >> 0x1d;
    *(byte *)((long)pvVar2 + (long)counter) =
         __block[counter] ^ (byte)local_48[(int)((counter + uVar3 & 7) - uVar3)];
    counter = counter + 1;
  }
```

I will be honest I couldn't completly reverse it, nor did I understand what is going on in the assembly code, this is too confusing.
for example, what the hell is `uVar3 = (uint)(counter >> 0x1f) >> 0x1d;`, shr 31 and then shr 29? this should just always give 0, from my understanding,
though in the assembly there is only `SHR EDX, 0X1d` instruciton, no 0x1f.

The assignment is confusing as well, `byte)local_48[(int)((counter + uVar3 & 7) - uVar3)]` like what is going on here,`uvar3` looks like is always 0
but no it should be different value. Anyway I feel like what it does is XORing each byte of the array with `local_48[counter % 8]`. Just to check that I run
GDB and see what are the first values of the result from the encryption. since I for sure know it begin with "CTFlearn{".

The encrypt flag first value is 'W', and in GDB it is indeed 'W', I also know that the first character is 'C' but 'C' XOR 'W' isn't 1, but 20. 
I still didn't check what the shuffle does so let's see it.

![ghidra2](https://user-images.githubusercontent.com/29147077/159575623-570bf4bc-1242-4ea0-9475-c234a6577ca6.jpg)

Now this one is much easier to undestand, basically it switches each 2 neighbour items (0 with 1, 2 with 3 and so on). Now that makes sense, the 'W' is
'T' xored with 3, now that I understand how it was encrypted, I make a python script do decrypt it.

```
keys = [1, 3, 3, 7, 0xde, 0xad, 0xbe, 0xef]
for i in range(0, len(encrypted), 2):
    encrypted[i], encrypted[i+1] = encrypted[i+1], encrypted[i]

for i in range(len(encrypted)):
    encrypted[i] = chr(encrypted[i] ^ keys[i % 8])

print("".join(encrypted))
```
* encrypted is just the same array from the main function.

result of the script is **CTFLearn{reversing_is_fun}**
