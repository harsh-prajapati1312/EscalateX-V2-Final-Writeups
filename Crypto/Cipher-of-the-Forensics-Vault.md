# Writeup: Cipher of the Forensics Vault

## Challenge Information
* **Challenge:** Cipher of the Forensics Vault
* **Category:** Crypto / Forensics
* **Artifact:** `vault_log.txt`

## Description
In 1997, a rogue cryptographer named Elijah Kross designed a secure offline vault hidden behind a layered encryption scheme. His journal contains scattered notes and cipher fragments. One phrase repeats: *"Only those who know my sequence of trust can open the echo vault."*

## Solution

### Step 1: Initial Analysis
The challenge provides the following content in `vault_log.txt`:
```text
Gur frperg vf: Fzg1o3Ntp29wM3xtrKOmL2qgoUVhVREmpTI2LJxtLJZtqz9mVRqdqaZtD295ozRhPxumo2f6VSA3MJu6MKMfGUgJJHx1A18kAI80K1N0EwAJsD==
```
The prefix `Gur frperg vf:` is a classic indicator of ROT13. Decoding `Gur frperg vf:` yields `The secret is:`.

### Step 2: Base64 Decoding
The remaining text ends with `==`, strongly indicating Base64 padding.

*   **Payload:** `Fzg1o3Ntp29wM3xtrKOmL2qgoUVhVREmpTI2LJxtLJZtqz9mVRqdqaZtD295ozRhPxumo2f6VSA3MJu6MKMfGUgJJHx1A18kAI80K1N0EwAJsD==`

Decoding this payload reveals the next layer:
```
Plaintext
Jkuop socgy ypscgmlr. Dspevai ac vos Gjvs Coyna.
Hsok: SwehzevlL{VYI57_15_4_P4F3V}
```

### Step 3: Vigenère Cipher
The decoded text is English but scrambled, suggesting a polyalphabetic substitution cipher.

*   **Hint:** The description emphasizes the phrase "open the echo vault".
*   **Cipher:** Vigenère
*   **Key:** `ECHO`

We verify this by testing known plaintext structures (like "Flag").

*   **Encrypted:** `Hsok`
*   **Key:** `CHOE` (shifted to align with the text start)
*   **Decrypted:** `Flag`

Decrypting the main text with key `ECHO` gives:
```
Plaintext
Final score recorded. Welcome to the Zero Vault.
Flag: EscalateX{VYI57_15_4_P4F3V}
```

### Step 4: Decrypting the Final Flag
The content within the flag braces `{}` remains encrypted. In standard Vigenère, non-alphabet characters are usually skipped. However, in this variation, symbols and numbers advance the key index even if they are not encrypted themselves.

Applying the `ECHO` key continuously across the entire string:
*   `...EscalateX` ends on key index `O`.
*   `{` uses key index `E`.
*   The flag content starts on key index `C`.

| Encrypted | Key       | Decrypted |
| :-------- | :-------- | :-------- |
| `VYI57`   | `CHOEC`   | `TRU57`   |
| `_15_4_`  | `HOECHOE` | `_15_4_`  |
| `P4F3V`   | `CHOEC`   | `L4Y3R`   |

The inner string decrypts to leetspeak: `TRU57_15_4_L4Y3R`.

### Final Flag
```
Plaintext
EscalateX{TRU57_15_4_L4Y3R}
```
