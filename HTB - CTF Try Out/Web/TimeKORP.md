# TimeKORP
![Image](https://github.com/user-attachments/assets/ef507e52-95d0-4170-924c-4255f36b1a14)

**Attachment:** [web_timecorp.zip](https://github.com/user-attachments/files/19540751/web_timecorp.zip)

## Writeup
![Image](https://github.com/user-attachments/assets/55e4514e-5049-4097-8bc7-4caf79019796)

"TimeKORP" is a simple site that displays the current time and date.

Looking at the source code, I found that `TimeController.php` is vulnerable to **Command Injection**:

![Image](https://github.com/user-attachments/assets/d8173e3b-ef4d-49e0-bb95-25a3f5304073)

![Image](https://github.com/user-attachments/assets/80806ee3-bdae-4876-a580-a9e0fcd34772)

![Image](https://github.com/user-attachments/assets/e4c5704e-5d2e-4e13-825a-4907559a2844)

Basically, we can control anything between the single quotes since the HTTP parameter is **unsanitized** and flows into `exec`:

![Image](https://github.com/user-attachments/assets/fa5635ce-b8a7-43b3-be18-68bdd25d8aac)

That being said, I can now create my payload and get the flag:

```
%Y-%m-%d';cat /flag;#
```
It's important to URL-encode it using `CTRL + U` to obtain a response.

![Image](https://github.com/user-attachments/assets/5c4cbe44-b13a-4445-9b9a-527b0d957ed0)

**HTB{t1m3_f0r_th3_ult1m4t3_pwn4g3_a2d2720e5fa0821944b7aa94302fb1da}**

