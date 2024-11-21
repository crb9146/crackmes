simple ctf by slice
https://crackmes.one/crackme/66ee30341070323296555610

This is a Linux ELF executable. The executable is not packed. This is a classic style CTF which challenges you to find a flag (a password). We are told that the flag stars with "CTF_". Running the binary and trying a password results in the following:

```
./simple_ctf 
D0 50M3 H4CK3r r3V
password
7rY 4641N
```

Typing the wrong password results in the message: "7rY 4641N" (Try Again in 1337speak). When you open the binary in Ghidra it takes you to the entry point (which is the main function). This function contains the majority of the code for the challenge. This function is what prints the message "D0 50M3 H4CK3r r3V" telling you to insert a password and contains the code for reading and comparing your input. Immediately in this function you can see lots of variables with seemingly random hex values defined in the stack.

At `0x00101357` you can see a read function which reads STDIN for user input. It reads 15 characters from the input (and stores this input into a 15 byte buffer), so the password we need to find is 15 characters long. It will also compare against a buffer with 15 len later when it checks if the password input is correct.

There is a function called `ctfhash` within this function. This symbol is already present in the binary, so Ghidra names this function for us. It takes in our 15 byte input buffer, another buffer of 65 bytes long, and an int variable which is 0xf, or 15. The second buffer (65 bytes long one) is most likely a result buffer and is just where the result of this `ctfhash` function is placed. The int variable is most likely just a length, since we already know the password required is 15 bytes long this value makes sense. Inside this `ctfhash` function, it modifies the bytes in the input buffer and places them in the result buffer. Due to the name `ctfhash` and it's contents, we can guess this is some sort of custom "hash" function which takes our input and modifies it into something else.

After this `ctfhash` function, there is another function called `compare_hashes` which takes in the result buffer (from the `ctfhash` function mentioned above) and the stack variables starting at 0x00101296. These stack variables are 15 different hex numbers placed on the stack one after the other, so this is most likely the password. This function just compares our input with these hex values on the stack, so to find the password we have to do the opposite of what encoding/hashing is being done in the `ctfhash` function.

`ctfhash` takes every value in the input buffer, multiples it by 100, then subtracts 10 from that. You also subtract a value (let's say i) for the number iteration you are on, starting from 0 from the initial hex value. So all we have to do is take all those numbers found on the stack and to the opposite of this to find the password. Add 10 - i and divide by 100. Doing this for all values results in the ASCII string "CTF_kittbyyy :D".

For example, the first 3 of these stack hex values are `0x1a22` (6690), `0x20c7` (8391) and `0x1b50` (6992). Here is the math for these:

(6690 + 10)/100 = 67 

(8391 - 1 + 10)/100 = 84 

(6992 - 2 + 10)/100 = 70 

Converting these decimal values to ASCII results in "CTF", so we know we are on the right track. Continue for the entire flag.

The flag is `CTF_kittbyyy :D`

```
./simple_ctf 
D0 50M3 H4CK3r r3V
CTF_kittbyyy :D     
Y4Y U D1D 17
```
