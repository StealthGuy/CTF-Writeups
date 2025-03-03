# Unfurl
![Image](https://github.com/user-attachments/assets/95aef574-bae7-46c2-aab7-d6b2b50bc989)

**Attachment:** [challenge.zip](https://github.com/user-attachments/files/19055047/challenge.zip)
**Password:** snyk-ftf-2025

## Writeup

![Image](https://github.com/user-attachments/assets/727cf9ff-8a67-4873-be48-c10cddbc877c)

Looking at the source code, I can see that there's a filter for the `clientIP` in adminRoutes.js to ensure only the local host would access it.  
In this specific case, I'm looking for an **SSRF vulnerability** that gives me command execution.

![Image](https://github.com/user-attachments/assets/d0ea2a35-eb57-4d11-81bc-036029ea8d23)

Looking closely at admin.js, I can see that the internal app is running on a random port.  
I don't know the port, but I do know the range, which makes it easy to enumerate:

![Image](https://github.com/user-attachments/assets/0c32da75-c29c-4641-8e05-fbdcb26e7456)

I used **BurpSuite** and **FoxyProxy** to intercept the POST request, I sent it to the **Intruder**, I modified the URL with "localhost", I added a **payload** to the **port** to find the right port and I started a **Sniper attack**:

![Image](https://github.com/user-attachments/assets/00ff3a59-71bf-4074-b959-b8174e920eb2)

In the **Payload tab**, I set the Payload type on **Numbers** and I added the **range**, in this case from *1024* to *5000* with step *1*:

![Image](https://github.com/user-attachments/assets/5a47a4ad-0f39-4e6f-b8fd-6cb3101646c9)

Finally, with this URL I was able to get the flag:

```bash
http://localhost:1217/execute?cmd=cat /usr/src/app/flag.txt
```

**flag{e1c96ccca8777b15bd0b0c7795d018ed}**
