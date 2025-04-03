# Hidden Message-Board
![Image](https://github.com/user-attachments/assets/f5f693cb-4b45-46fc-9fcc-7c8de86b50cf)

## Writeup
![Image](https://github.com/user-attachments/assets/f99f15ef-0e53-4072-9297-7faf1807a565)

"Hidden Message-Board" is a forum-style group chat. We are given instructions on how to format our text.

Based on the challenge descryption, I knew I had to inspect the site using with **DevTools**. While looking at `App.js`, I found this strange code:

`G1v3M3Th3Fl@g!!!!`

![Image](https://github.com/user-attachments/assets/88d64d41-e5db-4fa3-9a26-83afe32fae76)

This code is used somewhere in the HTML. Further inspection reveals a hidden `div` tag with the **ID** `flagstuff` and an empty `code` value.

![Image](https://github.com/user-attachments/assets/6f251d91-3923-4bb6-b876-c7c1b430bf91)

By editing the HTML code and inserting the previewed content into the `code` value, the site returns the flag.

![Image](https://github.com/user-attachments/assets/4ab69f3b-d48f-4e63-809d-f4b10888e00e)

**swampCTF{Cr0ss_S1t3_Scr1pt1ng_0r_XSS_c4n_ch4ng3_w3bs1t3s}**
