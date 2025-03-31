# Beginner Pwn 1
![Image](https://github.com/user-attachments/assets/c6bc469b-ab96-44dc-83f2-d4328cbaffd7)

**Attachment:** [challenge.zip](https://github.com/user-attachments/files/19528592/challenge.zip)

## Writeup

![Image](https://github.com/user-attachments/assets/c4412262-699a-414f-9d09-f55fe469d428)

After connecting with Netcat, we are provided with a web challenge based on **Buffer Overflow**.
We must provide a username, but our goal is log in as admin to retrieve the flag. To achieve this, we need to manipulate values in the program's memory to trigger the desired behavior.

![Image](https://github.com/user-attachments/assets/6dc25bfe-77ea-463a-b296-ddba34907f8c)

The `is_admin` variable is a 4-byte array that likely determines administrator privileges: it currently has all values ​​set to `0x00`, so we are not an admin.
The `username` variable is a 10-byte array. Due to the proximity of the two variables in memory, we can log in as admin and get the flag by inserting a username longer than 10 bytes (10 characters) and overwriting the values ​​of the `is_admin` variable.

![Image](https://github.com/user-attachments/assets/f9f2e8c3-4ec2-4605-8f46-125595ead651)

**swampCTF{n0t_@11_5t@ck5_gr0w_d0wn}**
