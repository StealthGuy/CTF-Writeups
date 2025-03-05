# Plantly
![Image](https://github.com/user-attachments/assets/84b07f97-e79f-41c9-9f41-b0062d97fde1)

**Attachment:** [challenge.zip](https://github.com/user-attachments/files/19072797/challenge.zip)  
**Password:** snyk-ftf-2025  
**Credentials:**
- testuser1@example.com:password123
- testuser2@example.com:mypassword456

## Writeup

![Image](https://github.com/user-attachments/assets/9d233db4-336a-4e4c-abe1-cb9788543613)

"Plantly" is an online plant store. We are provided with two sets of default/example user credentials for the initial login, and additional accounts can be registered if needed.

Create an account for yourself or sign in using the provided credentials. Navigating to "Browse Our Plants," I can see a selection of plants available for adding to the cart, along with a "Custom Order" field to enter text:

![Image](https://github.com/user-attachments/assets/79b4af0d-86d7-4313-9b7e-ee017cb3af37)

If I add a random plant to my cart, enter a random "Custom Order Request", and proceed to checkout, I will see a summary of my order along with an option to view the receipt:

![Image](https://github.com/user-attachments/assets/9ae91944-19c4-453a-b2f0-e57493697b0d)

![Image](https://github.com/user-attachments/assets/043dbbf5-38e4-436a-ab1d-95ae49aaf902)

![Image](https://github.com/user-attachments/assets/621c8f94-5f41-40c9-b702-56eccf989706)

Interestingly, viewing the receipt displays my "Custom Request" text as part of the receipt:

![Image](https://github.com/user-attachments/assets/085f3991-2569-4c9b-a0d0-7a20e874b759)

Looking at the source code, I can already see the potential for **SSTI vulnerability** in `source.py` as **unsanitizied user input** is passed to `render_template_string`:

![Image](https://github.com/user-attachments/assets/79558027-4618-4eb1-93d8-1a2488df1ef5)

![Image](https://github.com/user-attachments/assets/94309e89-1a71-43de-80ae-9178e428b4ce)

This means I can control this input. The web app is based on Flask, which is a Python web framework uses Jinja2 as its template engine, so I tested it using this basic payload for Server-Side Template Injection as shown here [here](https://hacktricks.boitatech.com.br/pentesting-web/ssti-server-side-template-injection#jinja2-python): 

```bash
{{7*7}}
```

![Image](https://github.com/user-attachments/assets/a31a5f39-5ca2-4cd9-a1ea-082555e29346)

![Image](https://github.com/user-attachments/assets/71dae25c-bf97-414c-84c6-e052a8c87449)

It worked! This means the payload is being evaluated by the server and reflected back to the user.  
After reading some documentation [here](https://hacktricks.boitatech.com.br/pentesting-web/ssti-server-side-template-injection#jinja2-python), [here](https://www.onsecurity.io/blog/server-side-template-injection-with-jinja2/), and [here](https://swisskyrepo.github.io/PayloadsAllTheThings/Server%20Side%20Template%20Injection/Python/#jinja2-remote-command-execution), I crafted this simple payload to get the flag:

```text
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('cat flag.txt').read() }}
```

![Image](https://github.com/user-attachments/assets/f9a57784-1998-4ebd-9019-0b870c3812eb)

**flag{982e3b7286ee603d8539f987b65b90d4}**
