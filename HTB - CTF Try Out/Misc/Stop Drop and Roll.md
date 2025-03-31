# Stop Drop and Roll
![Image](https://github.com/user-attachments/assets/9abc1bb1-ef26-43a2-b550-8d4d3abcf852)

## Writeup

After connecting to the challenge using **Netcat** and testing it, it's clear that we need to script it to retrieve each character of the flag.

![Image](https://github.com/user-attachments/assets/39becb39-183a-4bf5-9ec3-d8738adb2262)

With this simple Python script, I can retrieve the flag:

```python
import pwn

pwn.context.log_level = "debug"

p = pwn.remote("94.237.57.230", "38442")

p.sendlineafter("(y/n)", "y")
p.recvuntil("Ok then! Let's go!\n")

while True:
    line = p.recvline().decode().strip()
    line = line.replace("GORGE", "STOP")
    line = line.replace("PHREAK", "DROP")
    line = line.replace("FIRE", "ROLL")
    line = line.replace(", ", "-")
    p.sendlineafter("What do you do? ", line)
```

![Image](https://github.com/user-attachments/assets/71abb05d-a789-407e-9b26-62c3d9b70612)

**HTB{1_wiLl_sT0p_dR0p_4nD_r0Ll_mY_w4Y_oUt!}**
