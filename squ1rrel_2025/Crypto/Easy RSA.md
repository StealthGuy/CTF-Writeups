# Easy RSA
![Image](https://github.com/user-attachments/assets/4c9b7fb1-bcb4-48ea-920b-4b93c007d13b)

**Attachment:** [EasyRSA.zip](https://github.com/user-attachments/files/19634133/EasyRSA.zip)

## Writeup

In this crypto challenge, we are provided with two files: `rsa_easy.py`, which contains the encryption code, and `easy_rsa.txt`, which includes the **RSA module** `n`, the **public exponent** `e`, and the **ciphertext** `c`.

```python
import random
from sympy import nextprime, mod_inverse


def gen_primes(bit_length, diff=2**32):
    p = nextprime(random.getrandbits(bit_length))
    q = nextprime(p + random.randint(diff//2, diff))
    return p, q


def gen_keys(bit_length=1024):
    p, q = gen_primes(bit_length)
    n = p * q
    phi = (p - 1) * (q - 1)

    e = 65537
    d = mod_inverse(e, phi)

    return (n, e)


def encrypt(message, public_key):
    n, e = public_key
    message_int = int.from_bytes(message.encode(), 'big')
    ciphertext = pow(message_int, e, n)
    return ciphertext


if __name__ == "__main__":
    public_key = gen_keys()

    message = "FLAG"
    ciphertext = encrypt(message, public_key)

    f = open("easy_rsa.txt", "a")
    f.write(f"n: {public_key[0]} \n")
    f.write(f"e: {public_key[1]} \n")
    f.write(f"c: {ciphertext}")
    f.close()
```

```
n: 26518484190072684543796636642573643429663718007657844401363773206659586306986264997767920520901884078894807042866105584826044096909054367742753454178100533852686155634326578229244464083405472076784252798532101323300927917033985149599262487556178538148122012479094592746981412717431260240328326665253193374956717147239124238669998383943846418315819353858592278242580832695035016713351286816376107787722262574185450560176240134182669922757134881941918668067864082251416681188295948127121973857376227427652243249227143249036846400440184395983449367274506961173876131312502878352761335998067274325965774900643209446005663 
e: 65537 
c: 14348338827461086677721392146480940700779126717642704712390609979555667316222300910938184262325989361356621355740821450291276190410903072539047611486439984853997473162360371156442125577815817328959277482760973390721183548251315381656163549044110292209833480901571843401260931970647928971053471126873192145825248657671112394111129236255144807222107062898136588067644203143226369746529685617078054235998762912294188770379463390263607054883907325356551707971088954430361996309098504380934167675525860405086306135899933171103093138346158349497350586212612442120636759620471953311221396375007425956203746772190351265066237
```

### Key Issue in Prime Generation

The `gen_primes` function generates two prime numbers `p` and `q` in the following way:

1. Generates a random prime `p` with `bit_length` bits
2. Generates `q` as the next prime after `p + random.randint(diff//2, diff)`, where `diff = 2³²`
3. This means `p` and `q` are **extremely close to each other**: their difference is at most **2³² (≈4 billion)**, which is **negligible** compared to their actual size (1024 bits, ≈10³⁰⁸)

### Fermat's Factorization Attack

We can exploit this proximity between `p` and `q` to factorize `n` using **Fermat's method**:

1. Since `p` and `q` are close, `n = p·q` will be very close to `((p+q)/2)²`
2. We can find integers `a` and `b` such that:
   - `n = a² - b²`, where:
       - `a = (p + q)/2`
       - `b = (q - p)/2`
3. This allows us to factorize `n` as:
   - `n = (a + b)(a - b)` (where `a + b = q` and `a - b = p`)
  
With the following Python, code I was able to retrieve the flag:

```python
import math
from sympy import mod_inverse

n = 26518484190072684543796636642573643429663718007657844401363773206659586306986264997767920520901884078894807042866105584826044096909054367742753454178100533852686155634326578229244464083405472076784252798532101323300927917033985149599262487556178538148122012479094592746981412717431260240328326665253193374956717147239124238669998383943846418315819353858592278242580832695035016713351286816376107787722262574185450560176240134182669922757134881941918668067864082251416681188295948127121973857376227427652243249227143249036846400440184395983449367274506961173876131312502878352761335998067274325965774900643209446005663
e = 65537
c = 14348338827461086677721392146480940700779126717642704712390609979555667316222300910938184262325989361356621355740821450291276190410903072539047611486439984853997473162360371156442125577815817328959277482760973390721183548251315381656163549044110292209833480901571843401260931970647928971053471126873192145825248657671112394111129236255144807222107062898136588067644203143226369746529685617078054235998762912294188770379463390263607054883907325356551707971088954430361996309098504380934167675525860405086306135899933171103093138346158349497350586212612442120636759620471953311221396375007425956203746772190351265066237

def fermat_factor(n):
    a = math.isqrt(n) + 1
    while True:
        b_square = a*a - n
        if b_square < 0:
            a += 1
            continue
        b = math.isqrt(b_square)
        if b*b == b_square:
            return (a - b, a + b)
        a += 1

p, q = fermat_factor(n)

# Calcolo la phi di Eulero
phi = (p - 1) * (q - 1)

# Calcolo la chiave privata d
d = mod_inverse(e, phi)

# Decifro il messaggio
message_int = pow(c, d, n)
message = message_int.to_bytes((message_int.bit_length() + 7) // 8, 'big').decode()

print("Flag:", message)
```

### Step-by-Step Explanation

1. **Factorization of n**: we use **Fermat's factorization** method to find `p` and `q` by exploiting the fact that they are close to each other
2. **Calculation of φ(n)**: φ(n) = (p-1)(q-1) (Euler's totient function)
3. **Calculation of the private key d**: d is the modular multiplicative inverse of e modulo φ(n)
4. **Decryption**: we compute `m = c^d mod n` and convert the resulting integer to a string

![Image](https://github.com/user-attachments/assets/a6b84dcb-249a-4a96-9246-4c89cc28f2e5)

**squ1rrel{who's_your_favorite_mathemetician?}**
