SSE Login by 5iriu5\
https://crackmes.one/crackme/65a81968eef082e477ff5d10

Asks for a username and password. This crackme is not written in C but directly in assembly. This is why Ghidra struggles with naming some of the function calls. Symbols are still present in the 'easy' version.

```
./easy 
username: user
password: pass
access denied!
```

In the binary you can see two memory locations labeled "secret" and "key" both 16 bytes long:\
**Secret:**  `42 75 84 a9 1a 75 83 d5 62 3e 8e 20 c5 fc f6 92`\
**Key:**     `d2 09 23 42 a5 10 79 d5 fb cf 2a 16 c5 fc f6 92`

By looking at the function, we can see that each character we inserted for username and password are added to a value in `key` . This is then compared with a byte in `secret`. For example, position 0 of the user given password is added to position 0 of key. Essentially the code is `user_input_password[0] + key[0] == secret[0];`. Note the `==`

The user inputted characters are compared to bytes in the secret byte array (with `==`). The result of the comparison (0 or 1) is stored in a 16 byte buffer which I labeled `comparison_buffer`. Code looks something like this for the first character of the username:
```
compare_buffer[0] = user_input_password[0] + key[0] == secret[0];
```

If every value of the comparison is correct for all the letters, everything should be 1 (in binary) which for the 16 bytes in hex is `0xffffffffffffffff`. The final part of the code checks for this. Similar to:
```
if (compare_buffer == 0xffffffffffffffff){
    win();
}
```

So we can get each byte of the username or password by subtracting the key byte from it's corresponding secret byte. (secret - key). If the value goes negative, it wraps around.

For each of these:

**Password:**\
0x62 - 0xfb = -0x99 (wraparound is 0x67 or `g`)\
0x3e - 0xcf = -0x91 (wraparound is 0x6f or `o`)\
0x8e - 0x2a = 0x64 (`d`)\
0x20 - 0x16 = 0xa (Newline `\n`)\
0xc5 - 0xc5 = 0 (Null terminator)\
0xfc - 0xfc = 0 (null)\
0xf6 - 0xf6 = 0 (null)\
0x92 - 0x92 = 0 (null)

Password is `god`.

**Username:**\
0x42 - 0xd2 = -0x90 (wraparound is 0x70 or `p`)\
0x75 - 0x09 = 0x6c (`l`)\
0x84 - 0x23 = 0x61 (`a`)\
0xa9 - 0x42 = 0x67 (`g`)\
0x1a - 0xa5 = -0x8b (wraparound is 0x75 or `u`)\
0x75 - 0x10 = 0x65 (`e`)\
0x83 - 0x79 = 0xa (Newline `\n`)\
0xd5 - 0xd5 = 0 (Null terminator)

Username is `plague`

It will print the message `hack the planet!`
```
./easy 
username: plague
password: god
hack the planet!
```

