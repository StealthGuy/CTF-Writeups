# An unusual sighting
![Image](https://github.com/user-attachments/assets/c1d27d3e-7039-4cd1-a8b6-6cab9941ebaf)

**Attachment:** [forensics_an_unusual_sighting.zip](https://github.com/user-attachments/files/19536743/forensics_an_unusual_sighting.zip)

## Writeup

After connecting to the challenge using **Netcat**, we can see that we need to answer some questions to get the flag.

We are provided with two **log files**: `bash_history.txt` and `sshd.log`.

![Image](https://github.com/user-attachments/assets/4d30c6ba-9785-4c99-b20f-6f9702f8bed8)

![Image](https://github.com/user-attachments/assets/380dd857-475e-4723-b6ea-2feb037f71ca)

![Image](https://github.com/user-attachments/assets/5f294f40-70aa-4598-9b3d-46ee7ce73fdf)

**Answers:**
- What is the IP Address and Port of the SSH Server (IP:PORT)
> 100.107.36.130:2221

- What time is the first successful Login
> 2024-02-13 11:29:50

- What is the time of the unusual Login
> 2024-02-19 04:00:14

- What is the Fingerprint of the attacker's public key
> OPkBSs6okUKraq8pYo4XwwBg55QSo210F09FCe1-yj4

- What is the first command the attacker executed after logging in
> whoami

- What is the final command the attacker executed before logging out
> ./setup

Flag: **HTB{4n_unusual_s1ght1ng_1n_SSH_l0gs!}**
