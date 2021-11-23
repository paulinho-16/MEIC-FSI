# Buffer Overflow Attack Lab (Set-UID Version)

## Task 1

Both of the programs seem to work the same, no output errors and both successfully run shell command

## Task 2

![Task 2 screenshot](images/Lab2Task2.png)

## Task 3

- Created *badfile*, compiled using make and used gdb to debug the file stack-L1-dbg
- Created a breakpoint in the function *bof*
- Write *run* to start executing the program, followed by *next* to execute a few instructions and stop after the **ebp** register is modified to point to the stack.
- Check **ebp** value using `p $ebp`: 0xffffcb98 -> 0xffffcfa8: 0xffffd1d8 
- Check the variable **buffer** value using `p &buffer`: 0xffffcb2c: 0xf7fcb3e0
- Substract the **ebp** address to the **buffer** address: 0x47c~
- Insert the shellcode in the *exploit<span></span>.py* file
- Exit the debug mode using `quit`

mudar shellcode para shell code arq 32
    - mudar start para algumas posições depois do return address (ver no gdb do executavel)
    - mudar o offset para 112 

