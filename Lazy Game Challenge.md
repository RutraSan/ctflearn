# Lazy Game Challenge (Easy)
challenge link:https://ctflearn.com/challenge/691

## Writeup
```
Rules of the Game :
(1) You will be Given 500$
(2) Place a Bet
(3) Guess the number what computer thinks of !
(4) computer's number changes every new time !.
(5) You have to guess a number between 1-10
(6) You have only 10 tries !.
(7) If you guess a number > 10, it still counts as a Try !
(8) Put your mind, Win the game !..
(9) If you guess within the number of tries, you win money !
(10) Good Luck !..

theKidOfArcrania:
  I bet you cannot get past $1000000!
```
Lets see, we start with 500$ and need to get to 1000000$. Playing fair will take long even if we win each time, but winning means to guess the number,
1/10 chance. Let's try betting more then we have
```
Place a Bet : 1000000 
Money you have : 500


You don't have that much
Enter only from what you have !.
```
Not possible. What about negative?
```
Place a Bet : -1000000
-----------------------
The Game is On, Good Luck !..

Make a Guess :
```
Alright, let's see if we can pass number that are not between 1 and 10.

I can't capture it, but it works. I will type 0 each time to lose and see what we get
```
Sorry you lost some money !..
Your balance has been updated !.
Current balance :  : 
1000500$
What the... how did you get that money (even when I tried to stop you)!? I guess you beat me!

The flag is CTFlearn{d9029a08c55b936cbc9a30_i_wish_real_betting_games_were_like_this!}
```
