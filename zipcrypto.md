# Zipcrypto challenge
- flag.zip
- the content of the file starts with "flag is: SK-CERT{"
## Breakdown of the zip
The zip contains a single file called password.txt, which is not openable without a password. The challenge itself and the hint are both pointing to the ZipCrypto encryption. 

## ZipCrypto
It is an encryption scheme built into the ZIP format, which uses a stream cipher based on:
- **three 32-bit keys(key0, key1, key2)** that start at a set values and are **generated according to the password set on zip**
```
the starting values
key0 = 0x12345678
key1 = 0x23456789
key2 = 0x34567890
```
- **CRC-32** - a linear function, which in this case is a vulnerability because of its predictability, 12 known plaintext bytes generate enough equations to solve for all three keys 
Let's say I've got a key0 = 0xABCD1234 and one byte from our file = 0x4B
We're going to take the lowest 8 bits of our number and we'll XOR them with a new byte(the first byte which is in the content of the file)
```
0xABCD1234 -> 0x34 -> 0x34 XOR 0x4B -> 0x7F
```
Now our number 0x7F will be looked up in the table and will be assingned a value which is already pre-calculated and always the same
```
crc[0x7F] = 0x2D02EF8D 
```
Now we will shift our key0 by 8 bits
```
0xABCD1234 >> 8 = 0x00ABCD12
```
Lastly we will XOR our results
```
0x00ABCD12
XOR
0x2D02EF8D
-----------
0x2DA922BF -> our new key0
```
So the flow is
```
byte updates key0 -> new key0
new key0 updates key1 -> new key1
new key1 updates key2 -> new key2

and the cycle repeats for each byte
key2 is then used to derive the keystream_byte 
```
The final flow
```
encrypted_byte = plaintext_byte XOR keystream_byte
```
We know that XOR is reversible so this should be easy to crack with a specialized tool (I used [bkcrack](https://github.com/kimci86/bkcrack/releases)).


## The solution
We must prepare the known-plaintext first
```shell
echo -n "flag is: SK-CERT{" > known.txt
```
Then we recover the three keys using bkcrack
```shell
./bkcrack -C flag.zip -c password.txt -p known.txt -o 0
```
And after that we decrypt the rest of the file
```shell
./bkcrack -C flag.zip -c password.txt -k 4cd3cc7f bd8a9331 e7ea787f -d decrypted.txt
```
And voilà flag is: SK-CERT{u51ng_700l5_15_b3773r_7h4n_gu3551ng}


