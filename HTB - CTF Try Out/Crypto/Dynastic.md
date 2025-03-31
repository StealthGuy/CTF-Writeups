# Dynastic
![Image](https://github.com/user-attachments/assets/8185627a-227c-4327-802e-abbc2d1c670f)

**Attachment:** [crypto_dynastic.zip](https://github.com/user-attachments/files/19535976/crypto_dynastic.zip)

## Writeup

In this challenge, we are provided with two files: `source.py`, which contains the encryption code, and `output.txt`, which holds the ciphertext. Our goal is to decrypt the ciphertext by reversing the provided script.

![Image](https://github.com/user-attachments/assets/daf55420-604a-4e33-a243-84a956b95838)

![Image](https://github.com/user-attachments/assets/64cd3072-26f2-4156-8d31-4a98b247fdc5)

With some simple modifications to the script, I can get the flag:

```python
ct = "DJF_CTA_SWYH_NPDKK_MBZ_QPHTIGPMZY_KRZSQE?!_ZL_CN_PGLIMCU_YU_KJODME_RYGZXL"

def to_identity_map(a):
    return a - 0x41

def from_identity_map(a):
    return chr(a % 26 + 0x41)

def decrypt(ct):
    c = ''
    for i in range(len(ct)):
        ch = ct[i]
        if not ch.isalpha():
            c += ch
        else:
            ech = to_identity_map(ord(ch) - i)
            chi = from_identity_map(ech)
            c += chi
    return c

print(decrypt(ct))
```

![Image](https://github.com/user-attachments/assets/b27fb113-3b57-4b9c-b51a-88ada6edec0c)

**HTB{DID_YOU_KNOW_ABOUT_THE_TRITHEMIUS_CIPHER?!_IT_IS_SIMILAR_TO_CAESAR_CIPHER}**
