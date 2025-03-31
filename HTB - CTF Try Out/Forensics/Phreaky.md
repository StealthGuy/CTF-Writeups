# Phreaky
![Image](https://github.com/user-attachments/assets/42cdbf19-b085-4773-bb21-206cb644009b)

**Attachment:** [forensics_phreaky.zip](https://github.com/user-attachments/files/19537235/forensics_phreaky.zip)  
**Password:** hackthebox

# Writeup

![Image](https://github.com/user-attachments/assets/2feac1b6-45ef-42d3-9de6-adeaa8b198fd)

We are provided with a Wireshark capture file. After analyzing it, I can see multiple emails being sent from `caleb@thephreaks.com` to `resources@thetalents.com`.

![Image](https://github.com/user-attachments/assets/909aa265-98a8-4c7d-aa83-d97d7c78d20a)

Analyzing the `DATA Fragment` packet,  I can see that the body of the email contains content encoded in **Base64**, along with a **clear-text password** and a **filename**.

![Image](https://github.com/user-attachments/assets/c3ef724c-addb-4a10-a019-f5bdecfc65be)

Based on the **file signature** decrypted using **CyberChef** (and obviously the filename on Wireshark), I determined that the content of the email is a `.zip` file.

![Image](https://github.com/user-attachments/assets/dfe9625b-9767-4db1-8499-d1006243cdb3)

![Image](https://github.com/user-attachments/assets/1acfdf82-05f8-44fd-b6cd-c97e52252544)

There are multiple emails, so I need to rebuild the entire PDF using all the parts I found.

In the terminal, I copied them using this command:

```bash
echo "content_of_the_email" | base64 -d > filename.zip 
```

![Image](https://github.com/user-attachments/assets/64bf436e-8897-410a-b134-d0eb262e22e6)

All I need to do now is unzip all the files and combine them into a single file.

![Image](https://github.com/user-attachments/assets/5323847e-c734-41aa-be62-425e62e54bd2)

```bash
cat phreaks_plan.pdf.part1 phreaks_plan.pdf.part2 phreaks_plan.pdf.part3 phreaks_plan.pdf.part4 phreaks_plan.pdf.part5 phreaks_plan.pdf.part6 phreaks_plan.pdf.part7 phreaks_plan.pdf.part8 phreaks_plan.pdf.part9 phreaks_plan.pdf.part10 phreaks_plan.pdf.part11 phreaks_plan.pdf.part12 phreaks_plan.pdf.part13 phreaks_plan.pdf.part14 phreaks_plan.pdf.part15 > phreaks_plan.pdf
```

![Image](https://github.com/user-attachments/assets/c80084ef-997b-4f07-b208-db3b05bfd982)

**HTB{Th3Phr3aksReadyT0Att4ck}**
