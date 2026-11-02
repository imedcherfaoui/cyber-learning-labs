# Cryptography Fundamentals

Cryptography ensures the confidentiality and integrity of data transmitted across networks.

## 1. Core Concepts
*   **Plaintext vs. Ciphertext:** Unencrypted readable data (plaintext) is converted into encrypted unreadable data (ciphertext) using a mathematical algorithm (cipher) and a key.
*   **XOR & Modulo:** Fundamental mathematical operations used at the bit-level to scramble data within modern ciphers.

## 2. Symmetric Encryption
Uses a single shared secret key to both encrypt and decrypt data.
*   **Pros/Cons:** Very fast and efficient, but key distribution is risky (if the key is intercepted, data is compromised).
*   **Standards:** AES (Advanced Encryption Standard) is the modern standard, replacing outdated and broken algorithms like DES and 3DES.

## 3. Asymmetric Encryption
Uses a mathematically linked key pair: a Public Key (shared openly) and a Private Key (kept secret). Data encrypted with the public key can only be decrypted by the private key.
*   **Pros/Cons:** Highly secure for key exchange and communication over untrusted networks, but computationally heavy and slower than symmetric encryption.
*   **Standards:** RSA, Diffie-Hellman, and Elliptic Curve Cryptography (ECC).
