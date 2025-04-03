# Preferential Treatment
![Image](https://github.com/user-attachments/assets/da4c2175-bc04-4e0c-81b6-e16b2d6d74c1)

**Attachment:** [gpnightmare.zip](https://github.com/user-attachments/files/19572742/gpnightmare.zip)

## Writeup
![Image](https://github.com/user-attachments/assets/13850331-023c-4757-a1da-90e93905c913)

Based on the challenge description, we are analyzing network traffic from an old `Windows Server 2008`, and our goal is to find the password.

Looking closely, there's an interesting `.xml` file called `groups.xml`. Following the TCP streams reveals that it contains sensitive information: a user, `Administrator`, and a password hash, `cpassword`.

![Image](https://github.com/user-attachments/assets/1a45da8e-1823-4c1f-9fdb-141bb29c964e)

Searching on the internet, I found that in `Windows Server 2008` (and all Windows versions that use **Group Policy Preferences**), the **AES key** used to encrypt `cpassword` is **fixed** and **publicly** known:

```
4e9906e8fcb66cc9faf49310620ffee8f496e806cc057990209b09a433b66c1b
```

With this command I can decrypt `cpassword` from **Group Policy Preferences (GPP)** using **OpenSSL** and the known **AES-256-CBC key**:

```bash
echo 'dAw7VQvfj9rs53A8t4PudTVf85Ca5cmC1Xjx6TpI/cS8WD4D8DXbKiWIZslihdJw3Rf+ijboX7FgLW7pF0K6x7dfhQ8gxLq34ENGjN8eTOI=' | base64 -d | openssl enc -d -aes-256-cbc -K 4e9906e8fcb66cc9faf49310620ffee8f496e806cc057990209b09a433b66c1b -iv 00000000000000000000000000000000 -nopad
```

Specifically:
- **`dAw7VQvfj9rs53A8t4PudTVf85Ca5cmC1Xjx6TpI/cS8WD4D8DXbKiWIZslihdJw3Rf+ijboX7FgLW7pF0K6x7dfhQ8gxLq34ENGjN8eTOI=`**: The `cpassword` hash value
- **`base64 -d`**: The type of decoding
- **`openssl enc -d -aes-256-cbc`**: Uses `AES-256-CBC` to decrypt the text
- **`-K`**: Specifies the `AES` key in `hexadecimal` format
- **`-iv`**: Specifies the **initialization vector (IV)**. `GPP` uses **IV=0** (16 bytes of `00`)
- **`nopad`**: Disables **padding**, because the ciphertext is already aligned correctly

![Image](https://github.com/user-attachments/assets/5023cf9e-55da-438f-848d-3420664da255)

With a **Python** code:

```python
import base64
from Crypto.Cipher import AES

def decrypt(cpassword):
    key = b'\x4e\x99\x06\xe8\xfc\xb6\x6c\xc9\xfa\xf4\x93\x10\x62\x0f\xfe\xe8\xf4\x96\xe8\x06\xcc\x05\x79\x90\x20\x9b\x09\xa4\x33\xb6\x6c\x1b'
    iv = b'\x00' * 16
    cipher = AES.new(key, AES.MODE_CBC, iv)
    
    decoded = base64.b64decode(cpassword)
    decrypted = cipher.decrypt(decoded)
    
    return decrypted.decode('utf-16').strip('\x00')

password = "dAw7VQvfj9rs53A8t4PudTVf85Ca5cmC1Xjx6TpI/cS8WD4D8DXbKiWIZslihdJw3Rf+ijboX7FgLW7pF0K6x7dfhQ8gxLq34ENGjN8eTOI="
print(decrypt(password))
```

![Image](https://github.com/user-attachments/assets/5e2604e3-9d32-4f88-a6a9-e50e2e93d46f)

**swampCTF{4v3r463_w1nd0w5_53cur17y}**
