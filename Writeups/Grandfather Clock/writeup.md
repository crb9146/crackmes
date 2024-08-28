Grandfather Clock by lodsb\
[https://crackmes.one/crackme/60db74bb33c5d410b88430dc](https://crackmes.one/crackme/60db74bb33c5d410b88430dc)

This binary is a classic CTF crackme. The flag is given as an argument on the command line. When running the binary with no arguments, it says:

`Usage: ./grandfather_clock <flag>`

If an argument is given which is not the flag, it says:

`That's not the flag!`

The flag string itself is obfuscated (along with all other strings in the binary the ones above).

At 0x00102020 there is 28 bytes of data. Specifically:
`5d 10 14 4c 43 10 43 4e 4d 14 3f 47 4c 34 23 26 41 5b 28 52 10 11 3f 53 11 4c 54 52`

This data is compared with your given argument string input using strcmp. Before this strcmp call, there is a function, which I labeled `argument_string_modifier` which modifies your given input string in some way. In order to find the flag, we have to take this data (At 0x00102020) and apply the reverse of what the function `argument_string_modifier` is doing.

This function basically starts at the 2nd to last character of the string, goes in reverse and hops over every character. For example:

`1, 2, 3, 4, 5`

Would become: 

`4, 2, 1, 3, 5`

It also subtracts 0x20 to every character, which we can reverse by adding 0x20 to every character. 
The 28 bytes at 0x00102020 with 0x20 added to it results in the string: `}04lc0cnm4_glTCFa{Hr01_s1ltr`

Doing the reverse of the scrambling shown above results in the flag: `CTFlag{_H4rm0n1c_0sc1ll4t0r}`

./grandfather_clock 'CTFlag{_H4rm0n1c_0sc1ll4t0r}'
Congratulations! You've cracked the code!

.gzf included in this repo, the view in Ghidra, create a new project, go to File -> Import File then select the .gzf
