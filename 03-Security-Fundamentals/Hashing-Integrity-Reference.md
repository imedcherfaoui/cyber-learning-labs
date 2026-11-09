# Hashing, Integrity, and Password Security (Reference Guide)

## 1. Hashing vs. Encryption
*   **Encryption** is a two-way function. Data is encrypted with a key and can be decrypted back to plaintext with a key. Used for *confidentiality*.
*   **Hashing** is a one-way mathematical function. It takes any amount of data and outputs a fixed-length string (the hash). It cannot be reversed. Used for *integrity* and password storage.
*   **Common Algorithms:** MD5 and SHA-1 (Deprecated/Insecure due to collisions). SHA-256 and SHA-512 (Modern standard).

## 2. Password Storage Vulnerabilities
*   **Rainbow Tables:** Massive pre-computed databases of plaintext passwords and their corresponding hashes. If a database is leaked, attackers just look up the hash in the rainbow table to find the password.
*   **The Fix (Salting):** A "Salt" is a random string added to the password *before* it is hashed (e.g., `Hash(Password + Salt)`). Because every user gets a unique salt, two users with the identical password will have completely different hashes, rendering Rainbow Tables useless.

## 3. Identifying Hashes
Automated tools (like HashID) can be unreliable. Context is key.
*   **Linux (`/etc/shadow`):** Requires root access. Hashes follow a strict format: `$prefix$options$salt$hash`.
    *   `$1$` = MD5
    *   `$2a$`, `$2b$`, `$2y$` = bcrypt
    *   `$6$` = SHA-512
    *   `$y$` = yescrypt (modern default)
*   **Windows (SAM Database):** Passwords are hashed using **NTLM** (a variant of MD4). They visually resemble MD5. Attackers often use tools like *Mimikatz* to dump these hashes from memory.

## 4. Hash Cracking Tools
Because hashes cannot be decrypted, they must be "cracked" by hashing massive lists of potential passwords (like the `rockyou.txt` wordlist) and comparing the outputs.
*   **Hashcat:** GPU-optimized. Best run on the host machine to leverage thousands of GPU cores for massive parallel processing.
    *   *Command syntax:* `hashcat -m [hash_type] -a [attack_mode] [hash_file] [wordlist]`
    *   *Example (MD5):* `hashcat -m 0 -a 0 hashes.txt rockyou.txt`
*   **John the Ripper:** CPU-optimized by default. Works easily out-of-the-box inside Virtual Machines.

## 5. HMAC (Integrity + Authenticity)
HMAC (Keyed-Hash Message Authentication Code) proves that a message hasn't been altered (Integrity) AND proves it came from someone with the secret key (Authenticity).
*   **The Math Problem:** Simple concatenation (`Hash(Key + Message)`) is vulnerable to Length Extension Attacks.
*   **The HMAC Solution:** It hashes the data twice using an Inner Pad (`ipad`) and Outer Pad (`opad`).
    *   `HMAC = Hash(Key_Outer + Hash(Key_Inner + Message))`
    *   This mathematical double-locking mechanism ensures attackers cannot append data to the message without invalidating the final hash.
