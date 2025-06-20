# WHO IS JH
![Image](https://github.com/user-attachments/assets/3020b1a6-c91c-4d28-bcb8-8e0290fca5eb)

**Attachment:** [challenge.zip](https://github.com/user-attachments/files/19043268/challenge.zip)  
**Password:** snyk-ftf-2025

## Writeup

![Image](https://github.com/user-attachments/assets/c965c8c5-d0ae-4614-ab9d-a87998345816)

"Who IS JH?" takes us to a page with several buttons, the most important ones being "Upload Evidence" and "The Conspiracy."

In the Dockerfile I can see where the flag is located:

![Image](https://github.com/user-attachments/assets/c9f0505b-c649-4d4c-a2d1-5f2cd9dfed4b)

In the same Dockerfile I can also see that some PHP functions are disable to prevent RCE:

![Image](https://github.com/user-attachments/assets/8bac8ecb-c9f4-4193-8f4c-9779d56130a7)

The listed functions are used to execute system commands, specifically:

- **exec($cmd)**: Executes a system command and returns the output as a string
- **system($cmd)**: Executes a command and immediately displays the output on the screen
- **shell_exec($cmd)**: Similar to exec(), but returns the entire output as a string
- **passthru($cmd)**: Executes a command and prints the output without buffering (useful for binaries with binary output)
- **popen($cmd, $mode)**: Opens a process and returns a handle to read/write to the command stream
- **proc_open($cmd, $descriptorspec, &$pipes)**: Similar to popen(), but allows more granular control over process input/output

Looking at the PHP source code, I can see that there is a pubblicy log file that I can access with directory `logs/site_log.txt`:

![Image](https://github.com/user-attachments/assets/04c1cda4-8b8d-4e33-891f-73f1238f3d9b)

I can upload an allowed image with a JPG, PNG, or GIF extension and see it in the log file:

![Image](https://github.com/user-attachments/assets/9f050206-19a3-489a-afa4-010ba85ed7e8)

With **curl** in the terminal:

![Image](https://github.com/user-attachments/assets/aa4d2c69-97d0-42f1-86e1-735805f17dde)

The image is uploaded with the original name and a `uniqid` generated by the code:

![Image](https://github.com/user-attachments/assets/3f05f070-dbdd-4c81-948d-26aadb256d70)

Anyway, the file upload is not very restricted. The **extension** is checked, but the **content** isn't:

![Image](https://github.com/user-attachments/assets/71ad9320-31ed-4a7a-8b86-afe682e1a0ea)

Continuing the code analysis, I can see that the `conspiracy.php` endpoint allows the user to include a file based on the language parameter:

![Image](https://github.com/user-attachments/assets/ca74b23e-72eb-4069-8139-b91d1d2f1e98)

![Image](https://github.com/user-attachments/assets/83fa5209-4bf5-4c5f-bbaf-8d83b8436fb5)

This endpoint, along with its `language` query parameter, is vulnerable. This gives me a valid attack chain: I can upload a `file.jpg` containing PHP code using Burp Suite, check the log to find the random name assigned to it, and then use the "conspiracy" endpoint to include and execute my code:

I used **Burp Suite** and **FoxyProxy** to intercept the **POST request**. I sent it to the **Repeater**, modified the **Content-Type** of the JPG image and I was able to successfully upload the image:

![Image](https://github.com/user-attachments/assets/ef4b7978-66a4-46da-8ca7-81d9302d644b)

![Image](https://github.com/user-attachments/assets/dfb434dd-0ce2-4229-b408-d05a6bb6859d)

It also works with this PHP payload:

```php
<?php echo file_get_contents("/flag.txt")?>
```

**flag{6558608db040d1c64358ad536a8e06c6}**
