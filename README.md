# Ransomware challenge
- ransomware.py
- slon.png.enc
- story.txt.enc
##  Breakdown of the .py file
![Ransomware.py](images/py.png)
```python
import os
from itertools import cycle
```
From the first lines, we notice libraries crucial for file management and repetetive cycles.
```python
TARGET_DIR = "./files/"
```
The target directory was the ./files 
```python
def encrypt(filename, key):
    orig_bytes = None
    encrypted_bytes = bytearray()
```
The encrypt function takes in the file and the key, then outputs the encrypted version of the file. The bytearray type is within the encrypted variable, it lets the programme to modify each byte individually.
```python
 with open(TARGET_DIR + filename, "rb") as f:
        orig_bytes = bytearray(f.read() )
```
This function opens the file in binary mode (rb) and then reads all bytes.
```python
encrypted_bytes = bytes(a ^ b for a, b in zip(orig_bytes, cycle(key)))
```
This is the most important step of the encryption. It pairs each byte from the file with the corresponding byte from the key and then overwrites it. This process will repeat until the whole file is rewritten. This process and the mathematical operator ^ indicate the XOR encryption.

XOR stands for exclusive OR (OR is a logical function), it operates like this:
| Bit X | Bit Y | X XOR Y |
| --- | --- | --- |
| 0 | 0 | 0 |
| 1 | 0 | 1 |
| 0 | 1 | 1 |
| 1 | 1 | 0 |

It requires bits to be different to return 1. This is how XOR operates with bits. With bytes (8 bits) it goes thoroughly bit by bit to encrypt every single byte. The XOR has a porperty called reversibility. Why?

**encryption** = plaintext ^ key 

plaintext = **encryption** ^ key

This means we can decrypt the files with the same key. 

```python
with open(TARGET_DIR + filename, "wb") as f:
        f.write(encrypted_bytes)

    os.rename(TARGET_DIR + filename, TARGET_DIR + filename + ".enc")

    print(f"[+] Encrypted {TARGET_DIR + filename}")

```
This function writes the encrypted bytes back into the original file in binary mode. 

```python
if __name__=="__main__":
    key = os.urandom(16)
    for subdir, dirs, files in os.walk(TARGET_DIR):
        for file in files:
            print(f"file name: {file}")
            encrypt(file, key)

```
The last part contains the key and block of tasks. The key will be randomly generated (16 bytes). The programme looks inside the specific directory and loops through all the files inside it, prints the filenames and encrypts them. 


## The solution
Our task will be to extract the headers -> one of the encrypted files we received was called slon.png.enc, with a file extension .png, which is going to be our reference point. PNG header is always the same (for format identification or for the detection of corruption of the file). That means we will have the encrypted header, the original header header + first chunk (the first chunk after the header is called IHDR, with specific bytes which we know), hense we'll be able to recover the key. 

PNG header (8 bytes) + IHDR chunk (8 bytes)
```
89 50 4E 47 0D 0A 1A 0A 00 00 00 0D 49 48 44 52
```

Encrypted header (16 bytes)
```
0A D2 9C D9 08 53 D5 65 21 BE 0C BF B0 36 1D 07
```
We are going to follow the rule:

**key** = encryption ^ plaintext



The key for decrypting is:
```
83 82 d2 9e 05 59 cf 6f
```















```python

```
