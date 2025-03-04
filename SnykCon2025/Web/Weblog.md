# Weblog
![Image](https://github.com/user-attachments/assets/1b931582-d280-444b-9859-8018c0889e94)

**Attachment:** [challenge.zip](https://github.com/user-attachments/files/19075785/challenge.zip)  
**Password:** snyk-ftf-2025

## Writeup

![Image](https://github.com/user-attachments/assets/163a5f1c-b68e-4513-8292-31a84216662b)

"Weblog" is a simple blogging app. The homepage features three main options: "Login", "Register", and "Recover Account".  
The first step is to go to "Register" and create an account:

![Image](https://github.com/user-attachments/assets/0cdfcb85-0a31-47ec-b4e3-3b6db6e933c4)

After logging in, I can see some new options: "Dashboard", "Search", "Blog", and "Create Post". I'll head straight to the search function:

![Image](https://github.com/user-attachments/assets/496929a9-a6a6-4cd6-9c9e-f7c5cac2bc5d)

While reviewing the application's source code, I found a search function in `search.py` that allows me to perform searches:

![Image](https://github.com/user-attachments/assets/fa9c2f64-a171-4ad2-bc74-ffe34bd7bbd5)

![Image](https://github.com/user-attachments/assets/796117f1-c67a-4d65-a53a-817e1756e513)

This specific code is vulnerable to SQL Injection, as confirmed by the [Snyk Extension](https://docs.snyk.io/scm-ide-and-ci-cd-integrations/snyk-ide-plugins-and-extensions/visual-studio-code-extension) for Visual Studio Code, and it directly dumps our query into the SQL query without any sanitization or checks.  
I verified that the app is vulnerable by searching for a **single apostrophe**, which returned me a detailed error message:


