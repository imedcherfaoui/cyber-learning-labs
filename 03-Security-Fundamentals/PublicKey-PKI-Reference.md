# Public Key Cryptography & PKI (Reference Guide)

## 1. Asymmetric Algorithms in Practice
Asymmetric cryptography uses a mathematically linked key pair: a **Public Key** (shared) and a **Private Key** (secret). It is slower than symmetric encryption but solves the problem of securely sharing keys.

*   **RSA (Rivest-Shamir-Adleman):** Primarily used for digital signatures and identity verification. Relies on the mathematical difficulty of factoring massive prime numbers.
*   **Diffie-Hellman (DH):** A key-exchange protocol. It allows two parties who have never met to mathematically agree on a shared symmetric key over an insecure channel. 
    *   *Python implementation concept:* `pow(base, exponent, modulus)`
*   **Modern usage:** Protocols like TLS use RSA to verify the server's identity, and Diffie-Hellman to safely generate the fast, symmetric session key used for the actual data transfer.

## 2. Digital Signatures & Integrity
A digital signature proves *who* sent a message (Authenticity) and that the message *was not altered* (Integrity). 
**How it works:**
1.  **Sender (Bob):** Hashes the document -> Encrypts the hash using his **Private Key** -> Sends document + encrypted hash.
2.  **Receiver (Alice):** Hashes the document -> Decrypts Bob's hash using his **Public Key** -> Compares the two hashes. If they match, the file is authentic and unaltered.

## 3. Certificates & PKI (Public Key Infrastructure)
A Digital Certificate (like a TLS/SSL certificate for HTTPS) binds a public key to an identity (a website or person).
*   **Chain of Trust:** A Certificate Authority (CA) verifies the organization. The CA signs the certificate. Your web browser trusts the CA, and therefore trusts the website's certificate.

## 4. SSH (Secure Shell) Authentication
SSH is used to securely log into remote Linux servers. Instead of passwords, we use SSH keys.
*   **Key Generation:** `ssh-keygen -t ed25519` (Generates a modern, secure key pair). Other types include RSA, DSA, ECDSA.
*   **How the server knows you:** You copy the text of your *Public Key* and paste it into a specific file on the remote server: `~/.ssh/authorized_keys`. 
*   **Rule:** The `.ssh` directory and private keys must have strict file permissions, or the system will reject them.

## 5. PGP & GPG (Pretty Good Privacy / GNU Privacy Guard)
Used primarily to encrypt, decrypt, and digitally sign emails and files.
*   **Import a key:** `gpg --import public_key.asc`
*   **Decrypt a file:** `gpg --decrypt file.gpg`
