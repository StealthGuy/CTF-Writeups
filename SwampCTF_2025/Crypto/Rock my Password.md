# Rock my Password
![image](https://github.com/user-attachments/assets/7b6fbc17-525f-43db-be83-72008e575511)

## Writeup

Before taking any active steps, let's first determine what needs to be done and how. It is known that a specific password from `rockyou.txt`, exactly **10 characters** long, was inserted into the `swampCTF{}` flag, and then the combined string was hashed.

The hashing process follows this order:
- 100 iterations of MD5
- 100 iterations of SHA-256
- 100 iterations of SHA-512

Obviously, iterating through all possible hashes would be extremely slow and impractical. Instead, we can take the following approach: extract all 10-character passwords from `rockyou.txt`, **hash** them in the specified order, and check if any of the resulting hashes match the given one:

`f600d59a5cdd245a45297079299f2fcd811a8c5461d979f09b73d21b11fbb4f899389e588745c6a9af13749eebbdc2e72336cc57ccf90953e6f9096996a58dcc`

This Python code loops through each **password**, and for those that are **10 characters** long, it calls the `multihash()` function to compute **300 hashes**. If the **resulting hash** matches the **target hash**, we've found our flag!

```python
import hashlib

def multihash(password):
    s = f"swampCTF{{{password}}}".encode('utf-8')
    # MD5 100 times
    for _ in range(100):
        md5 = hashlib.md5()
        md5.update(s)
        s = md5.digest()
    # SHA256 100 times
    for _ in range(100):
        sha256 = hashlib.sha256()
        sha256.update(s)
        s = sha256.digest()
    # SHA512 100 times
    for _ in range(100):
        sha512 = hashlib.sha512()
        sha512.update(s)
        s = sha512.digest()
    return s.hex()

target_hash = "f600d59a5cdd245a45297079299f2fcd811a8c5461d979f09b73d21b11fbb4f899389e588745c6a9af13749eebbdc2e72336cc57ccf90953e6f9096996a58dcc"

# we read rockyou.txt from current directory, take all passwords with length == 10, and then compute hash for each pass and compare with our target hash
with open('rockyou.txt', 'r', errors='ignore') as f:
    for line in f:
        password = line.rstrip('\n')  # Only remove the newline character
        if len(password) == 10:
            computed_hash = multihash(password)
            if computed_hash == target_hash:
                print(f"Found password: {password}")
                print(f"Flag: swampCTF{{{password}}}")
                exit()
    print("Password not found in the rockyou.txt dataset or there was an error...")
```

![image](https://github.com/user-attachments/assets/03039dc9-b973-4066-bab5-04e94e540c42)

**swampCTF{secretcode}**

