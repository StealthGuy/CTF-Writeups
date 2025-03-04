# TimeOff
![Image](https://github.com/user-attachments/assets/d6d176ee-7df6-40e1-9b80-ef877a49de67)

**Attachment:** [challenge.zip](https://github.com/user-attachments/files/19060874/challenge.zip)  
**Password**: snyk-ftf-2025  
**Credentials:**
- admin@example.com:admin123
- user@example.com:user123

## Writeup

![Image](https://github.com/user-attachments/assets/24509afe-6bdc-40ac-bbf1-d2527881f485)

Time Off is a time management system that allows you to book holidays. We are provided with default credentials to log in to the app; upon logging in as a regular user, we can view departments, other employees, and the option to request time off:

![Image](https://github.com/user-attachments/assets/ad5c4773-0a6e-465a-b4dc-a21ff6c837ae)

When we select "Request Time Off," we can enter our dates, provide a reason, and upload a document. Interestingly, we have the ability to choose the file name of the document. Looking at the source code with the help of **[Snyk Extension](https://docs.snyk.io/scm-ide-and-ci-cd-integrations/snyk-ide-plugins-and-extensions/visual-studio-code-extension)** for Visual Studio Code, I can see that the "file_name" parameter is vulnerable to Path Traversal:

![Image](https://github.com/user-attachments/assets/adf08440-c61e-4e94-90c0-2c5d3271012e)

Basically, if I use a Path Traversal as the file name I can write the contents of `flag.txt` into the file I upload, whatever it is. In this case, with the path indicated in the Dockerfile:

```bash
../../../../..//timeoff_app/flag.txt
```

So I used **Burp Suite** and **FoxyProxy** to intercept the **POST request**. I sent it to the **Repeater**, modified the **Content-Disposition** of the file and I was able to successfully upload it and read the flag:

![Image](https://github.com/user-attachments/assets/9d842bac-8b24-4bf0-b540-d8f621e446dc)

![Image](https://github.com/user-attachments/assets/879b1226-b77d-4bce-8769-8bd1f11e38cc)

![Image](https://github.com/user-attachments/assets/542f063d-9e90-47e0-ba8c-e69ea271d620)

**flag{52948d88ee74b9bdab130c35c88bd406}**
