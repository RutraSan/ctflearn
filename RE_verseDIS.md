# RE_verseDIS (Medium)
chellenge link: https://ctflearn.com/challenge/188

## Writeup
This chellenge shouldn't be medium. In fact, soled it in less then 5 minutes. The thing is this challenge is very similar to
[https://github.com/RutraSan/ctflearn/blob/main/Reykjavik.md](Reykavik which I solved before), and this was a piece of cake.

First I run the program normally as usual, and it asks for a password which I don't know. I open Ghidra and check the decompiler output.
As I said, this challenge is similar to the Reykjavik challenge, there is yet again a XOR decryption, and I am past my previous mistakes knew
what I have to do.

![ghidra1](https://user-images.githubusercontent.com/29147077/159483244-2133a0ba-7b0c-447d-84e7-0ebea7253c93.jpg)

I open GDB and set a breakpoint after the decryption, run the program and print the value of `msg`, challenge completed. 
