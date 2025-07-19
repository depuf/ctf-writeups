# Rule Breaker 1

**Category:** hash cracking

**Perceived Difficulty:** ★☆☆☆☆ (1/5)

This challenge provided three hashes corresponding to mutated versions of common passwords, along with the specific rules used to mutate each one.
The task was to reverse-engineer the mutations and recover the original passwords.

## First Hash

**Hash:**  5e09f66ae5c6b2f4038eba26dc8e22d8aeb54f624d1d3ed96551e900dac7cf0d

**Rule:**  Append 3 characters at the end, in the following order: a special character, a number, and an uppercase letter.

**Approach:** 
Using hashcat's hybrid mode to append the letters to each password in rockyou.txt:

```
hashcat -m 1400 -a 6 hash.txt rockyou.txt '?s?d?u'
```

where _-m 1400_ specifies that mode of attack is sha256, _-a 6_ specifies hybrid mode to append mask to wordlist, and _'?s?d?u'_ being the mask for the 
appended characters.

**Result:**  5e09f66ae5c6b2f4038eba26dc8e22d8aeb54f624d1d3ed96551e900dac7cf0d:hyepsi^4B

## Second Hash

**Hash:** fb58c041b0059e8424ff1f8d2771fca9ab0f5dcdd10c48e7a67a9467aa8ebfa8

**Rule:** A typo was made when typing the password. Consider a typo to mean a single-character deletion from the password

**Approach:** 
I thought it'd be faster if I just wrote a python script to generate all possible typos. This approach does take up space, however.


```
with open("rockyou.txt", "r", errors="ignore") as f:
    words = f.read().splitlines()

with open("typo_rockyou.txt", "w") as f:
    for word in words:
        for i in range(len(word)):
            f.write(word[:i] + word[i+1:] + "\n")
```

```
hashcat -m 1400 -a 0 hash.txt typo_rockyou.txt
```

**Result:** fb58c041b0059e8424ff1f8d2771fca9ab0f5dcdd10c48e7a67a9467aa8ebfa8:thecowsaysmo

## Third Hash

**Hash:** 4ac53d04443e6786752ac78e2dc86f60a629e4639edacc6a5937146f3eacc30f

**Rule:** Make the password leet (and since I'm nice, I'll tell you a hint: only vowels are leetified!)

**Approach:** 
Initially, I tried generating a python script to leetify all the vowels. However, I wasn't getting matches and it wasn't
because the script was wrong, but it was because I didn't consider @ as a substitute for a (obviously I didn't figure that out at the time) 
Instead, I went ahead and searched for a leetspeak rule file, and used that instead

**Credits:**  
[unix-ninja-leetspeak.rule](https://github.com/hashcat/hashcat/blob/master/rules/unix-ninja-leetspeak.rule) from Hashcat’s repo.


```
hashcat -m 1400 -a 0 -r unix-ninja-leetspeak.rule hash.txt rockyou.txt
```

**Result:** 4ac53d04443e6786752ac78e2dc86f60a629e4639edacc6a5937146f3eacc30f:unf0rg1v@bl3

## Flag
L3AK{hyepsi^4B_thecowsaysmo_unf0rg1v@bl3}

