# Character
![Image](https://github.com/user-attachments/assets/342d4413-faef-43e2-8183-b7d44d217de3)

## Writeup

![Image](https://github.com/user-attachments/assets/d8eca520-cee9-457f-98a9-383c0cef4d06)

After connecting to the challenge using **Netcat** and testing it, it's clear that we need to script it to retrieve each character of the flag.

With this simple Python script, I can retrieve the flag:

```python
import pwn

p = pwn.remote ("83.136.251.19", "53593")

flag = ""
i = -1
while True:
    i+=1
    p.sendlineafter("index: ", str(i))
    p.recvuntil(": ")
    flag += p.recvline().strip().decode()

    print(flag)
```

![Image](https://github.com/user-attachments/assets/3922b480-163b-4e55-b347-2367dbf0dea4)

**HTB{tH15_1s_4_r3aLly_l0nG_fL4g_i_h0p3_f0r_y0Ur_s4k3_tH4t_y0U_sCr1pTEd_tH1s_oR_els3_iT_t0oK_qU1t3_l0ng!!}**
