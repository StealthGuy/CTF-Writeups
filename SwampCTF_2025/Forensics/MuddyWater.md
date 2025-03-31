# MuddyWater
![Image](https://github.com/user-attachments/assets/d86703e0-bc6c-460a-acbc-b2cbf73bc7cf)

**Attachment:** [muddywater.zip](https://github.com/user-attachments/files/19533834/muddywater.zip)

## Writeup

![Image](https://github.com/user-attachments/assets/cfbd3622-18c9-418c-b309-9c7687dd70cd)

This is a Wireshark capture file of a threat actor brute-forcing an **SMB2** login. Our task is to find the correct login credentials, including the username and password used.

Using this filter, we can extract the correct login from the mass of failed attempts.

```
smb2 && smb2.nt_status == 0x00000000 && frame.len != 506
```

Specifically: 
- **`smb2`**: Filters only traffic related to the `SMB2` protocol
- **`smb2.nt_status == 0x00000000`**: Indicates that the `SMB2 Negotiate Protocol Response` has been successfully accepted (STATUS_SUCCESS)
- **`frame.len != 506`**: Filters by `Frame Length` different than 506 (to find the correct login)

![Image](https://github.com/user-attachments/assets/02fe436f-da3c-442b-9a78-9d456994fa44)

By following the **TCP stream**, we can see the full conversation:

![Image](https://github.com/user-attachments/assets/2f114c85-3a5e-487f-954d-cea90b30b5a9)

We can see the username, **hackbackzip**, but not the password, as it is encrypted by `NTLM`. To retrieve it, we need to find and assemble the **hash**, then crack it using **John the Ripper** or **Hashcat**.

The `NTLMv2` format is the following:

```
Username::Domain:Server Challenge:HMAC-MD5 (NTProofStr):NTLMv2Response (without HMAC)
```

![Image](https://github.com/user-attachments/assets/e1dd2427-9680-4afe-abb1-fd89f1f70466)

- **`Username`**: hackbackzip
- **`Domain`**: DESKTOP-0TNOE4V

![Image](https://github.com/user-attachments/assets/e311564f-05fb-4282-ae48-36a49e2be471)

- **`Server Challenge`**: d102444d56e078f4

![Image](https://github.com/user-attachments/assets/261cd0d1-18a7-4ba4-8caa-8ff876c2ad1c)

- **`HMAC-MD5 (NTProofStr)`**: eb1b0afc1eef819c1dccd514c9623201
- **`NTLMv2Response (without HMAC)`**: 01010000000000006f233d3d9f9edb01755959535466696d0000000002001e004400450053004b0054004f0050002d00300054004e004f0045003400560001001e004400450053004b0054004f0050002d00300054004e004f0045003400560004001e004400450053004b0054004f0050002d00300054004e004f0045003400560003001e004400450053004b0054004f0050002d00300054004e004f00450034005600070008006f233d3d9f9edb010900280063006900660073002f004400450053004b0054004f0050002d00300054004e004f004500340056000000000000000000

The complete **hash** is:

```
hackbackzip::DESKTOP-0TNOE4V:d102444d56e078f4:eb1b0afc1eef819c1dccd514c9623201:01010000000000006f233d3d9f9edb01755959535466696d0000000002001e004400450053004b0054004f0050002d00300054004e004f0045003400560001001e004400450053004b0054004f0050002d00300054004e004f0045003400560004001e004400450053004b0054004f0050002d00300054004e004f0045003400560003001e004400450053004b0054004f0050002d00300054004e004f00450034005600070008006f233d3d9f9edb010900280063006900660073002f004400450053004b0054004f0050002d00300054004e004f004500340056000000000000000000
```

Now we can crack it. I used **John the Ripper** with this command:

```bash
john --format=netntlmv2 --wordlist=rockyou.txt NTLMv2_hash.txt
```

![Image](https://github.com/user-attachments/assets/76d705e3-1df5-4337-aefe-b4d2308fbb7f)

I had already cracked the hash, so I used this command to display it from the `.pot` file:

```bash
john --show NTLMv2_hash.txt
```

**swampCTF{hackbackzip:pikeplace}**
