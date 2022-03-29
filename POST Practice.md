# POST Practice (Medium)
challenge link: https://ctflearn.com/challenge/114

## Writeup
This is actually a very easy challenge if you know about HTTP, I don't know why the author gave it a Medium. Let's begin.

>This website requires authentication, via POST. However, it seems as if someone has defaced our site. Maybe there is still some way to authenticate?

From the description alone we understand what is the challenge about. Make a POST request, and pass login information, the question is how it should look like and
what information we need to pass.

![web1](https://user-images.githubusercontent.com/29147077/160694281-c5ecd433-8c22-4085-aeca-426187bdfcbc.jpg)

In the page we don't see much, checking the source will give us the credentials for the login.

![web2](https://user-images.githubusercontent.com/29147077/160694453-bba2f7c9-282c-48c4-8152-69c40a8e66ac.jpg)

Nicely commented us ` username: admin | password: 71urlkufpsdnlkadsf ` so now we know what are the parameters we need to send, and what are their values. Let's do that.

### With Python and requests
First I solved it with python and the requests module so I will show it's solution.
```
import requests
url = "http://165.227.106.113/post.php"
d = {"username": "admin", "password": "71urlkufpsdnlkadsf"}
r = requests.post(url, data = d)
print(r.text)
```
As I said the challenge is very easy, it's not even a hard thing to learn how POST request works and what is it. But checking the comments I saw a mention of
[curl](https://curl.se/) which is a tool for sending request using URLs, and that might be a faster way to solve it (not that requests is bad, but knowing more tools
is good)

### With curl
As I said this is my first time using this tool, but it's actually not complicated, the final function looked like this:

`curl  "http://165.227.106.113/post.php" -d "username=admin&password=71urlkufpsdnlkadsf"`

Comparing it to python, this is indeed much faster to make and execute, so I guess I will use it more often for simple HTTP requests or any other URL based protocols.
I will also explain what this function does if you are interested:
* `curl` is of course the program we run
* Next is the url, which doesn't have to be at the begging, `curl  -d "username=admin&password=71urlkufpsdnlkadsf" "http://165.227.106.113/post.php"` this would also
work
* `-d` for data, this will also automaticly inform `curl` that this is POST requests (GET doens't have body).
* The data of the Post request.

My conclusion is that curl is a powerful command tool and I am definitely will use it in the future.
