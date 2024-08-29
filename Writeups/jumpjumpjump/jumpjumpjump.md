jumpjumpjump By cbm-hackers\
[https://crackmes.one/crackme/5c1a939633c5d41e58e005d1](https://crackmes.one/crackme/5c1a939633c5d41e58e005d1)

.gzf of this file included in this repo. To view in Ghidra, create a new project, go to File -> Import File then select the .gzf

The binary is not packed and strings are present at first glance. 

When running it asks for a password:

```
./rev03 
enter the magic string
<password>
wrong string
No flag for you.
```

The code for this crackme takes place in the main function, so finding out where to start is pretty simple. The first thing is the `puts` function printing the string seen above, `enter the magic string`. This is followed by a `fgets` call which takes the user inputted string from stdin and stores it in a buffer of 112 bytes.

The second thing it does is call `strlen` on the input, then there is an if statement checking if this value is less than 12. If it is 12 or more characters it will print the message: `too long...sorry no flag for you!!!`

Next, there is a do while loop with a counter variable. This loop will continue for the length of the length of the string you inserted. There is also a variable, which I labled `total`, that takes the value of the character and adds it up. Basically it gets the total of all the characters. Next it checks if this total is equal to 1000. This is the "win condition" which will print the flag. There is some attidional code after this, specifically a function called `strcat_str`, but we don't have to focus on reversing this.

Note that the function that calcuates the total includes the newline character, 0xa or 10. So your characters that you need to insert need to add up to 990 + `0 (or 0xa).

There can be multiple answers. I did `dddddddddZ`. `d` is `0x64` and `Z` is `0x5A`. So the math is `(0x64 * 9) + (0x5a) + (0xa) = 1000`. You could also make it simplier and do `n` 9 times. This says the flag is: `!#&*/5<DMW`

```
./rev03 
enter the magic string
dddddddddZ
flag is flag{!#&*/5<DMW}
```

I also did patch this to see if it did print the flag anyway, just by inserting NOPs over a JMP. This did work. Just for fun but not a real solution.
