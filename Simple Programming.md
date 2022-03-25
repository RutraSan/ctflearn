# Simple Programming (Eazy)
challenge link: https://ctflearn.com/challenge/174

## Writeup
I mean, just the code since the question is simple and understandable :/
```
c = 0
for line in open("data.dat").readlines():
    c+= int(line.count("0") % 3 == 0 or line.count("1") % 2 == 0)
print(c)
```
