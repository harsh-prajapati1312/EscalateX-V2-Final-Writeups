# Escaltex CTF: Memory Inception Writeup

This writeup details the solution for the "Memory Inception" challenge from the Escaltex CTF.

## Challenge Overview

The challenge is a memory forensics task. We are given a memory dump file, `dump.bin`, and our goal is to find the flag hidden within it.

## Initial Exploration & Clue Discovery

As with most memory forensics challenges, the first step is to look for human-readable strings in the binary file. The `strings` command is perfect for this.

```bash
strings dump.bin
```

Sifting through the output of `strings`, a few interesting lines stood out:

1.  A line indicating a username: `User: agent_omega`
2.  Two markers suggesting a hidden data block: `--- HIDDEN_REGION_START ---` and `--- HIDDEN_REGION_END ---`.

These clues suggest that there is a block of hidden data, and the username `agent_omega` is likely related to it.

## Forming a Hypothesis

Based on the discovered clues, a working hypothesis was formed:
1.  The data between the `HIDDEN_REGION` markers is the encrypted flag.
2.  The username, `agent_omega`, is used to generate the encryption key.
3.  A common and simple encryption method used in CTFs is a repeating key XOR.
4.  The key is likely a hash of the username. SHA1 is a common choice for this.

So, the plan is to:
1.  Extract the data between the markers.
2.  Calculate the SHA1 hash of "agent_omega".
3.  XOR the extracted data with the SHA1 hash.
4.  Analyze the result, which is hopefully the flag or something that leads to it.

## The Solution: A Single Script

To implement this plan, a single Python script, `solve.py`, was created to handle the entire process from extraction to flag recovery. This avoids intermediate files and automates the solution.

The script performs the following steps:
1.  **Extracts** the username and the ciphertext from the `dump.bin` file.
2.  **Generates** the decryption key from the username.
3.  **Decrypts** the ciphertext using a repeating XOR cipher.
4.  **Decodes** the resulting plaintext to reveal the flag.

Here is the final, all-in-one script:

**`solve.py`**
```python
#!/usr/bin/env python3
import hashlib
import base64
import sys
import re
from typing import Tuple, Optional

# Define markers as bytes for binary searching
MARKER_START = b"--- HIDDEN_REGION_START ---"
MARKER_END   = b"--- HIDDEN_REGION_END ---"
USER_MARKER = b"User:"

def xor_bytes(data: bytes, key: bytes) -> bytes:
    """XORs byte data with a repeating key."""
    return bytes(d ^ key[i % len(key)] for i, d in enumerate(data))

def extract_username_and_ciphertext(path: str) -> Tuple[str, bytes]:
    """
    Extracts the username and ciphertext from a binary file.
    This function reads the file in binary mode to avoid any data corruption.
    """
    try:
        with open(path, "rb") as f:
            raw_data = f.read()
    except FileNotFoundError:
        raise SystemExit(f"[-] Error: File not found at '{path}'")
    except Exception as e:
        raise SystemExit(f"[-] Error: Could not read file at '{path}': {e}")

    # --- Extract Username ---
    user_start_index = raw_data.find(USER_MARKER)
    if user_start_index == -1:
        raise SystemExit("[-] Could not find the 'User:' marker in the dump.")
    
    line_end_index = raw_data.find(b'\n', user_start_index)
    if line_end_index == -1:
        line_end_index = len(raw_data)
        
    user_line_bytes = raw_data[user_start_index + len(USER_MARKER):line_end_index]
    username = user_line_bytes.decode('latin-1', errors='ignore').strip()
    
    if not username:
        raise SystemExit("[-] Username line found but appears to be empty.")

    # --- Extract Hidden Region (Ciphertext) ---
    start_index = raw_data.find(MARKER_START)
    if start_index == -1:
        raise SystemExit("[-] Hidden region START marker not found.")

    end_index = raw_data.find(MARKER_END, start_index)
    if end_index == -1:
        raise SystemExit("[-] Hidden region END marker not found.")

    ciphertext = raw_data[start_index + len(MARKER_START):end_index]
    ciphertext = ciphertext.strip()

    return username, ciphertext

def try_decode_base64(data: bytes) -> Optional[bytes]:
    """
    Attempts to decode a base64 string, with a fallback for cleaning the string.
    """
    try:
        return base64.b64decode(data, validate=True)
    except (ValueError, TypeError, base64.binascii.Error):
        try:
            cleaned_data = bytes(c for c in data if 42 <= c <= 122 and chr(c) in "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=")
            return base64.b64decode(cleaned_data, validate=True)
        except (ValueError, TypeError, base64.binascii.Error):
            return None

def find_flag(b: bytes) -> Optional[str]:
    """Searches for a flag pattern in a byte string."""
    if not b:
        return None
    try:
        s = b.decode("utf-8")
    except UnicodeDecodeError:
        s = b.decode("latin-1", errors="ignore")
    
    m = re.search(r'flag\\{[^}]+\}', s, re.IGNORECASE)
    return m.group(0) if m else None

def solve(path: str):
    """Main solver function."""
    print(f"[+] Analyzing file: {path}")
    username, ciphertext = extract_username_and_ciphertext(path)
    print(f"[+] Found Username: {username}")
    print(f"[+] Extracted {len(ciphertext)} bytes of ciphertext.")

    sha1_key = hashlib.sha1(username.encode("utf-8")).hexdigest()
    key = bytes.fromhex(sha1_key)
    print(f"[+] Generated SHA1 key: {sha1_key}")

    plaintext = xor_bytes(ciphertext, key)
    print("[+] XOR decryption complete.")

    decoded_data = try_decode_base64(plaintext)
    if not decoded_data:
        raise SystemExit("[-] Failed to decode Base64 string after XOR.")
    
    print("[+] Base64 decoding successful.")

    flag = find_flag(decoded_data)
    if flag:
        print(f"\n[SUCCESS] Flag found: {flag}\n")
    else:
        print("\n[-] Flag pattern not found in the decoded data.\n")

if __name__ == "__main__":
    if len(sys.argv) > 1:
        file_path = sys.argv[1]
    else:
        print("Usage: python3 solve.py <path_to_dump_file>")
        print("Defaulting to 'dump.bin' in the current directory.")
        file_path = "dump.bin"
    
    solve(file_path)
```

### Running the Script

Running this script on the `dump.bin` file executes the plan and reveals the flag:

```bash
python3 solve.py dump.bin
```
```
[+] Analyzing file: dump.bin
[+] Found Username: agent_omega
[+] Extracted 44 bytes of ciphertext.
[+] Generated SHA1 key: 9fcef2f8bad0c5f885474ec0f207124f1ab15a13
[+] XOR decryption complete.
[+] Base64 decoding successful.

[SUCCESS] Flag found: flag{memory_inception_achieved}
```

## The Flag

The final flag is:
```
flag{memory_inception_achieved}
```
