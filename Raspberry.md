# Raspberry
challenge link: https://ctflearn.com/challenge/1080

## Writeup
This is a challenge for beginners in assembly language, and as stated could be easily solved with Ghidra or IDA, so we won't do that and will use GDB
instead and reverse the assembly code.

after getting the program inside GDB and checking the assembly code, you will notice that there are different labels of same structure _FirstLetter, 
_SecondLetter and so on. In total there are 19 letters or characters in the flag. I know it starts with "CTFlearn{" so I can save myself 9 characters.

This is actually very simple program, for each character in the inputed string, it does different kind of manipulations using `add, sub, mul, div, shl`
instructions, those it's very easy to reverse doing the opposite action, in the end we get the flag **CTFlearn{+Fruit123}**.
