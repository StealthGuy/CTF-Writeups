# Shush Protocol
![Image](https://github.com/user-attachments/assets/2785807c-286c-4109-9caf-19f12a619d1c)

**Attachment:** [ics_shush_protocol.zip](https://github.com/user-attachments/files/19535465/ics_shush_protocol.zip)

## Writeup

This challenge provided a `traffic.pcapng` file with the goal of finding a flag.

![Image](https://github.com/user-attachments/assets/8f5d0e31-78a3-4a7c-aa3e-484be93a9af0)

All the captured traffic is based on the `Modbus/TCP` protocol.

While examining the file, I observed a significant number of packets with `FUNCTION CODE = 102`, which is not a common **Modbus** function code.

![Image](https://github.com/user-attachments/assets/372105f1-c6d0-47a4-b790-ec8624145c92)

Each of these packets contained a line of data, but one in particular was noticeably longer than the others **(Length = 123)**.

![Image](https://github.com/user-attachments/assets/148fbad7-547c-4755-8bca-5dd11504de1a)

Alongside the raw data, there is an **ASCII** representation of the content. I then converted the entire hexadecimal string into text using **CyberChef** and successfully retrieved the flag.

![Image](https://github.com/user-attachments/assets/4c6d0990-89e7-4c53-b30f-3af98fb716fc)

**HTB{50m371m35_cu570m_p2070c01_423_n07_3n0u9h7}**
