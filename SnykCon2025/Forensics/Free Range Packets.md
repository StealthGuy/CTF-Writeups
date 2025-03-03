# Free Range Packets
![Image](https://github.com/user-attachments/assets/ca491d84-43ca-433c-a686-324326f494f3)

**Attachment:** [freeRangePackets.zip](https://github.com/user-attachments/files/19052067/freeRangePackets.zip)

## Writeup

Looking at the challenge description, I knew I had to work with the **btl2cap protocol** (Bluetooth Logical Link Control and Adaptation Protocol L2CAP).
With **tshark** (the command line version of Wireshark) and some further **grepping** and **cutting** I can get the flag.

```bash
tshark -r freeRangePackets.pcapng -Y 'btl2cap.payload' -T fields -e btl2cap.payload 2>/dev/null | grep -v '09ff01065c' | cut -c 7- | cut -c 1-2 | xxd -r -p
```

Specifically:

- tshark -r freeRangePackets.pcapng: Reads the capture file `freeRangePackets.pcapng`
- -Y 'btl2cap.payload': Filters packets for those containing the `btl2cap.payload` (Bluetooth L2CAP payload) field
- -T fields -e btl2cap.payload: Prints only the value of the `btl2cap.payload` field
- 2>/dev/null: Suppresses any error messages by redirecting `stderr` to `/dev/null`
- grep -v '09ff01065c': Excludes lines containing `09ff01065c` from the result
- cut -c 7-: Removes the first 6 characters of each line
- cut -c 1-2: Selects only the first 2 characters of the remaining line
- xxd -r -p: Converts hexadecimal output to binary format

![Image](https://github.com/user-attachments/assets/a7cb61b8-c469-4a04-a1a3-147266bd72b0)

**flag{b5be72ab7e0254c056ffb57a0db124ce}**
