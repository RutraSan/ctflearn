# Basic Android RE 1
[https://ctflearn.com/challenge/962](link to challenge)

## Writeup
I am not experience with ARK rev, so this one actually took me some time altough this challenge is very easy.
I figured I need to decompile the APK first and tried [javadecompilers.com](http://www.javadecompilers.com/apktool) which works, and I tried to use
Andoird Studio (apks are for android, that made sense to me) but I don't know how to use it, and the search worked weird, but I belived that it's the files
that were decompiled poorly, so I downloaded [apktool](https://ibotpeaches.github.io/Apktool/) and used it do decompile the files, this time I also opened
them in VSCode instead and used Android Studio to run the program.

This time using VSCode I actually found what I wanted using the search option. I searched for "flag" and found the strings file that I actually found in
Android studio as well. I don't know why, but VSCode just gave me the IQ boost and I figured to find the button widget and it's function. I foind the `smali`
code of that function, it hashed the password and compared it to a const hash value. Quickly I knew I need to find the decrypt value of the hash and thus found
what the password should be. Entered it and got the flag.
