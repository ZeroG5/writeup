# Ransomware challenge
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
This fuction writes the encrypted bytes into the original file in binary mode. 


The key
```python

```
```python

```
