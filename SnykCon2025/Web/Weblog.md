# Weblog
![Image](https://github.com/user-attachments/assets/1b931582-d280-444b-9859-8018c0889e94)

**Attachment:** [challenge.zip](https://github.com/user-attachments/files/19075785/challenge.zip)  
**Password:** snyk-ftf-2025

## Writeup

![Image](https://github.com/user-attachments/assets/163a5f1c-b68e-4513-8292-31a84216662b)

"Weblog" is a simple blogging app. The homepage features three main options: "Login", "Register", and "Recover Account".  
The first step is to go to "Register" and create an account:

![Image](https://github.com/user-attachments/assets/0cdfcb85-0a31-47ec-b4e3-3b6db6e933c4)

After logging in, I can see some new options: "Dashboard", "Search", "Blog", and "Create Post". I'll head straight to the **search** function:

![Image](https://github.com/user-attachments/assets/496929a9-a6a6-4cd6-9c9e-f7c5cac2bc5d)

While reviewing the application's source code, I found a search function in `search.py` that allows me to perform searches:

![Image](https://github.com/user-attachments/assets/fa9c2f64-a171-4ad2-bc74-ffe34bd7bbd5)

![Image](https://github.com/user-attachments/assets/796117f1-c67a-4d65-a53a-817e1756e513)

This specific code is vulnerable to **SQL Injection**, as confirmed by the [Snyk Extension](https://docs.snyk.io/scm-ide-and-ci-cd-integrations/snyk-ide-plugins-and-extensions/visual-studio-code-extension) for Visual Studio Code, and it directly dumps our query into the SQL query without any sanitization or checks.  
I verified that the app is vulnerable by searching for a **single apostrophe**, which returned me a detailed error message:

![Image](https://github.com/user-attachments/assets/4f77287e-bcbf-4a9a-a734-045cce8b9685)

Now, even without the source code, I can see where my input is being injected into the query. Fortunately, since I have the source code for this app, I can skip some of the enumeration steps. I know there are **two tables**, and that they have the same number of **columns**. In `models.py`, I can see:

![Image](https://github.com/user-attachments/assets/3f8f6548-5fba-40bb-8d52-29316eb239a7)

There’s also a comment here indicating that the passwords are **MD5 hashed**, so I knew I needed to focus on dumping the users table.  
I used this **UNION Injection** payload:

```SQL
' UNION SELECT * FROM users-- 
```

![Image](https://github.com/user-attachments/assets/5d5c7fbc-ad6d-4b7f-9408-91f74f71f8af)

![Image](https://github.com/user-attachments/assets/13e1f99e-e475-4feb-a9c6-ea9d4c9b4e4c)

Now that I got the MD5 hash for the admin account, I dumped it into **John the Ripper** to get the password:

```text
john --format=raw-md5 --wordlist=rockyou.txt hash.txt
```

![Image](https://github.com/user-attachments/assets/012cd03e-4545-48f6-820a-e1641e9409c4)

Now I can logout and login again as **admin** using the cracked password.  
There is a new button: "Admin Panel". Inside, I can see a "Rebuild Database Command" which I am able to edit:

![Image](https://github.com/user-attachments/assets/f50931f0-549b-4339-a87e-79f73ecd350b)

However, there are restrictions in field. In the source code under `admin.py` I can see:

![Image](https://github.com/user-attachments/assets/b5e2db4f-fc0f-4344-a435-8d93e831dcc5)

Here, I can see that we're not allowed to use any `DISALLOWED_CHARS`, and the command **must** start with `echo 'Rebuilding database…' && /entrypoint.sh`. One notable character that isn’t on the disallowed list is the **semicolon**. A semicolon acts as a command terminator and can separate two commands, so my payload is:

```bash
echo 'Rebuilding database...' && /entrypoint.sh; cat flag.txt
```

![Image](https://github.com/user-attachments/assets/01903ff0-7181-48b2-911e-20d3b152b38b)

**flag{b06fbe98752ab13d0fb8414fb55940f3}**
