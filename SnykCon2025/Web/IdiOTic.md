# idi0Tic
![Image](https://github.com/user-attachments/assets/fa8576c2-7648-4cc3-bdaf-7f54c3d04b41)

**Attachment:** [challenge.zip](https://github.com/user-attachments/files/19094161/challenge.zip)
**Password:** snyk-ftf-2025

## Writeup

![Image](https://github.com/user-attachments/assets/4eeed24e-ff59-4f7f-bc55-58910d53c8fa)

I need to find a login, and after going through the `Java` source code, in `CustomUserDetailsService.java` I found the **credentials** for the default user:

![Image](https://github.com/user-attachments/assets/38966ea3-77a2-4843-91d0-2f15816603d3)

By further examining the source code with the **[Snyk Extension](https://docs.snyk.io/scm-ide-and-ci-cd-integrations/snyk-ide-plugins-and-extensions/visual-studio-code-extension)** for Visual Studio Code, I discovered a High Severity vulnerability caused by the **Deserialization of Untrusted Data**:

![Image](https://github.com/user-attachments/assets/383ba5e3-4a37-4943-a4cb-77b0be18ae9c)

I can also see from the Dockerfile that the application is using **Java 17 JDK**:

![Image](https://github.com/user-attachments/assets/859a1de5-a5b7-4661-ab16-fe5d6c5e1932)

Upon logging in, I see an IoT Controller Dashboard with an "Add Device" option among others:

![Image](https://github.com/user-attachments/assets/8caef21f-c175-4ebc-9e85-845231bc68da)

So, the `/upload` endpoint is responsible for the deserialization:

![Image](https://github.com/user-attachments/assets/d84599e0-bb56-4168-bac0-f499457eab26)

There's another interesting section on the site where I can generate `.bin` files:

![Image](https://github.com/user-attachments/assets/1f256759-8757-4d1a-a89b-163c8e86b3bf)


