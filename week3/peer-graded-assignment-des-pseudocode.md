# Peer Graded Assignment: DES Pseudo-Code

### Which programming language, if any, are you familiar with?

I am familiar with Java and Python.

### Write your answer for the pseudo-code for the DES encryption.

The DES Algorithm requires many smaller structures such as the Substitution Box, Permutation Box,
The Mixer and Swapper which Compose a Single Round of Feistel Cipher etc. We describe all structures 
below:

#### Permutation Box (PBOX)

```text
PBox:
    key = [5 4 78 45 .... 64] // an n length key for permuting a given sequence

    function permute(sequence):
        result <-- new sequence
        for index, element in sequence:
            result[key[index]] = element
        endfor
        return result
    endfunction
```

#### Substitution Box (SBox)
In the substitution box we will have a table that will provide us with mappings from 2 dimensional
bit tuples to a 4-bit binary sequence and we obtain the 2 dimensional tuple from a given binary 
sequence.

We compose our `SBox` with a `func` which is a function which returns `(row, column)` tuple from
binary input. 

```text
SBox:
    table <-- provided by user
    func <-- function for getting (row, column) from binary input

    function substitute(binary)
        row, column = func(binary)
        return table[row][column]
    endfunction
```

#### Swapper 
Swapper is a simple Cipher part of a single round of the DES Cipher except the last round. The last round 
in the DES cipher contains no swapper component. The swapper imply takes in a binary sequence and 
splits it into 2 and then swaps both components to return a new binary sequence.

```text
Swapper:
    function encrypt(sequence):
        L <-- left component of sequence
        R <-- right component of sequence
        result <-- R + L
        return result
    endfunction

    function decrypt(sequence):
        // performs the same swapping operation as the encryption part
        return encrypt(sequence)
    endfunction
```

#### Mixer
This is the most important component of the single round in the DES algorithm. In the mixer we obtain a
64 bit binary sequence. We divide it into 2 parts of 32 bit each. The right part is then expanded
using an expansion Permutation Box (E-PBox) and a resulting 48 bit sequence is created.

The resulting 48 bit sequence is used with the key with a non-reversible mathematical
function, in this case the mod function to obtain a new 48 bit sequence.

8 Substitution boxes are applied to the 48 bit sequence to reduce it down to 32 bit. Each SBox
reduces 6 bit --> 4 bit. Another permutation is applied to the right block.

Finally a XOR (^) operation is performed between left and right parts and the result is stored in
the new left part (32 bits). The combination of these pieces are then returned as 64 bit binary 
sequence which then go to the Swapper.  

```text
Mixer:
    function encrypt(sequence):
        L <-- left part of sequence (32 bit)
        R <-- right part of sequence (32 bit)
        
        R1 <-- apply expansion PBox on R
            
        // apply non-invertible function
        R2 <-- apply non-invertible modular function on R1 using key K_i
        
        // apply substitution boxes on R2 (48 bit --> 32 bit)
        R3 <-- apply 8 substitution boxes on R2 

        // apply XOR (^) on left and right
        L1 <-- L ^ R3

        // return combined result
        return L1 + R3
    endfunction
    
    // same steps as encryption
    function decrypt(sequence):
        return encrypt(sequence)
    endfunction
```

#### Round
We now define a single round of the DES Cipher which is in fact a single round of the Feistel 
Cipher.

```text
Round:
    mixer <-- the mixer component with key k_i
    swapper <-- The swapper component part of a single round

    function encrypt(sequence)
        ciphertext = mixer.encrypt(sequence)
        ciphertext = swapper.encrypt(ciphertext)
        return ciphertext
    endfunction

    function decrypt(sequence)
        plaintext = swapper.decrypt(sequence)
        plaintext = mixer.decrypt(plaintext)
        return plaintext
    endfunction
``` 

We now combine 16 such rounds to form the DES Ciphers where the last round has no Swapper component
and the first 15 rounds have Swappers + Mixers. Also we have a key generator which yields 16 
different keys for each round.

```text
DES:
    rounds <-- generate 15 rounds with 15 different keys using the key generator
    rounds <-- add a 16th round with just the Mixer component

    function encrypt(sequence):
        ciphertext <-- initial permutation on sequence
        for round in rounds [i = 1... 16]
            ciphertext = round.encrypt(ciphertext)
        endfor 
        return final permutation on ciphertext
    endfunction 


    function decrypt(sequence):
        plaintext <-- final permutation inverse on sequence
        for round in rounds [i = 16... 1] // going in reverse
            plaintext = round.decrypt(plaintext)
        endfor
        return initial permutation inverse on plaintext
    endfunction
```
