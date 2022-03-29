# Basic Injection  (Easy)
challenge link: https://ctflearn.com/challenge/88

## Writeup
So this challenge should teach about SQLi, which I really have not yet studied. Let's begin.
I used (this source)[https://www.w3schools.com/sql/sql_injection.asp] to learn about it, and turns out it is not that complicated as I feared it is. I have a little
expirience and understanding of SQL so my study was not long, the poinst is to make the request valid for all, for example `1=1` is always true, so the server will return
all of the items.

I start by typing random thing and get `Your Resulting Query: SELECT * FROM webfour.webfour where name = 'aaa'`. This challenge is really friendly and shows what is 
the resulting query, which helps a lot. the call looks something like this `SELECT * FROM ... where name = '<input>'` the problem is the `'` at the end, we need to
make the query valid, but not really a big riddle, my resulting input is `'' or 'a'='a` and the missing `'` will be added by the server. 
