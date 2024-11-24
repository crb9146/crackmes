baby's first crackme by mystic_rust\
https://crackmes.one/crackme/66736380e7b35c09bb266f92

Even though this crackme is called "baby's first" I would say this crackme is a bit of a harder challenge. The difficulty level of this on the site is rated at 2.9/6. When you run the binary it asks you for a key and number. It prints `Access denied!` when you try something incorrect.

```
./rust-1 
usage: ./rust-1 <key> <number>
````

At `0x00101418` there is a strlen call which takes the length of the key argument. If the len is equal to 12, it moves on to another function called `check_key()`, so the key len must be equal to 12 (otherwise there is an else block which prints `Access denied!`). Key len must be 12.

`check_key` takes both the key and number as a parameter. Inside of this `check_key` function there is an `encode_input` function which takes the key parameter and modifies it. It stores the result in a a separate buffer. After this function is called, an if statement checks if the last character of the key input equals 0x7c. This means only the last character of key matters, our input is modified some way in `encode_input`, so we have to manipulate the last character to make it equal to 0x7c after the encode function runs. It still has to be 12 characters long, so we pad every other character than the last with any character (I just used '2', but you can use anything).

In this encode function, there are lots of branches the code can take for modifying the key. There is a for loop for each character and modifies the character in some way. We only have to focus on the last char (i = 11 in the for loop for position 12 in key). There is an if statement checking if each char is an even or odd number. The char we used at position 11 is 'z', which is 0x7a or 122, even. So in this branch, it will take the character, multiply our `number` arg by 2 and that number to the char 'z' (our input). Since our number we inserted is 1, 1 * 2 = 2, and we add 2 to 0x7a (z) which is 0x7c. So we have the final check, last character is 0x7c which is a success.

There are many keys that work, I used `22222222222z 1`. `2` is just filler.

```
./rust-1 22222222222z 1
Access granted!
```
