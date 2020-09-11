# Honors Peer Graded Assignment: Pseudo-Code Vignere Cipher

### Which programming language, if any, are you familiar with?

I am familiar with Java and Python.

### Write your answer for the pseudo-code for the Vigenere Cipher encryption.

We take the plaintext as p and ciphertext as c which we produce from plaintext during encryption and
we produce the plaintext back from the ciphertext using the decryption steps. We encrypt the plaintext 
p of arbitrary length using key stream generated from key k of arbitrary length.

such that c_i = (p_i + k_i) mod 26 where k_i = key_{i mod m} where m is the length of the key. The 
pseudo-code is represented as:

```text
m <-- length of key 
for index, character in plaintext:
    ciphertext[index] <-- (character + key[index % m]) % 26

return ciphertext
```

This encryption algorithm can be represented in Python as 

```python
def encrypt(plaintext: str, key: str) -> str:
    ciphertext = ''
    m = len(key)
    for i, letter in enumerate(plaintext.lower()):
        ciphertext += chr((char_2_num(letter) + char_2_num(key[i % m])) % 26 + ord('a'))
    return ciphertext.upper()

def char_2_num(character: str) -> int:
    return ord(character.lower()) - ord('a')
```
   
where `char_2_num` is a utility function. Similarly we decrypt the ciphertext using the key by the 
following pseudo-code:

```text
m = length of key
for index, character in ciphertext:
    plaintext[i] <-- (character - key[index % m]) % 26

return plaintext
```

The algorithm in Python is represented as:

```python
def decrypt(ciphertext: str, key: str) -> str:
    plaintext = ''
    m = len(key)
    for index, letter in enumerate(ciphertext.lower()):
        plaintext += chr(
            (char_2_num(letter) - char_2_num(key[index % m])) % 26 + ord('a'))
    return plaintext.lower()
```
