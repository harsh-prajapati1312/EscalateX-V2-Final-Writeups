# CTF WRITEUP: Encrypted Ledger (Automated Approach)

**Challenge Name:** Encrypted Ledger
**Flag:** `CTF7{pdf_comm3nt_tr4p}`

## CHALLENGE DESCRIPTION

A dusty ledger was digitized and stored as a PDF — but someone quietly hid
a final entry by tacking extra bytes onto the end of the file after the
official end-of-file marker. The PDF itself opens fine in viewers, but the
real secret waits past the secret. Your task is to locate and extract that
appended block and reveal the flag.

## SOLUTION: AUTOMATED EXTRACTION SCRIPT

Instead of manually inspecting the file with command-line tools, this
solution uses a Python script to automatically find and extract the hidden
flag from the PDF file. The script searches for the PDF End-Of-File (EOF)
marker, `%%EOF`, and then reads the data appended after it.

## PYTHON SCRIPT

Here is the Python script used to solve the challenge:

```python
#!/usr/bin/env python3

def find_appended_data(file_path):
    """
    Finds and returns data appended after the last %%EOF marker in a file.
    """
    try:
        with open(file_path, 'rb') as f:
            content = f.read()
            # The PDF EOF marker can have preceding characters on the line
            # so we search for the last occurrence of b'%%EOF'
            eof_marker = b'%%EOF'
            last_eof_pos = content.rfind(eof_marker)

            if last_eof_pos == -1:
                print("Error: %%EOF marker not found.")
                return None

            # The data starts after the marker itself.
            # The marker is often followed by a newline.
            appended_data_start = last_eof_pos + len(eof_marker)
            
            # We can refine by looking for a newline
            if content[appended_data_start] in (b'\n'[0], b'\r'[0]):
                 appended_data_start += 1
                 if content[appended_data_start] in (b'\n'[0], b'\r'[0]) and content[appended_data_start] != content[appended_data_start-1]:
                     appended_data_start += 1


            if appended_data_start < len(content):
                return content[appended_data_start:].decode('utf-8', 'ignore')
            else:
                print("No data found after %%EOF marker.")
                return None

    except FileNotFoundError:
        print(f"Error: File not found at {file_path}")
        return None
    except Exception as e:
        print(f"An error occurred: {e}")
        return None

def main():
    """
    Main function to extract the flag from the encrypted ledger.
    """
    pdf_file = 'encrypted_ledger.pdf'
    print(f"[*] Analyzing '{pdf_file}' for appended data...")
    
    appended_text = find_appended_data(pdf_file)

    if appended_text:
        print("[+] Found appended data:\n")
        print("---------------------------------")
        print(appended_text.strip())
        print("---------------------------------")
        
        # Extracting the flag from the appended block
        for line in appended_text.splitlines():
            if "CTF7" in line:
                print(f"\n[!] Flag found: {line.strip()}")
                break

if __name__ == '__main__':
    main()
```

## HOW THE SCRIPT WORKS

1.  **File Reading**: The script opens `encrypted_ledger.pdf` in binary mode (`'rb'`) to ensure it can read all bytes without any text encoding issues.

2.  **Finding EOF**: It uses `content.rfind(b'%%EOF')` to find the position of the *last* `%%EOF` marker in the file. This is important because a PDF could technically have the string `%%EOF` as part of its content. We are interested in the final one that marks the end of the valid PDF structure.

3.  **Slicing the Data**: Once the marker is found, the script slices the file's content from the end of the marker to the actual end of the file.

4.  **Decoding and Printing**: The extracted bytes are decoded into a UTF-8 string (ignoring any errors) and printed. The script then specifically searches for the line containing the flag pattern "CTF7".

## RUNNING THE SCRIPT

To get the flag, you would save the code above as `solve.py`, give it execute permissions, and run it:

```bash
   $ chmod +x solve.py
   $ ./solve.py

   [*] Analyzing 'encrypted_ledger.pdf' for appended data...
   [+] Found appended data:

   ---------------------------------
   %%FLAGSTART
   CTF7{pdf_comm3nt_tr4p}
   %%FLAGEND
   ---------------------------------

   [!] Flag found: CTF7{pdf_comm3nt_tr4p}
```

## LESSONS LEARNED

-   **Automation is Key**: For repetitive or common forensic tasks like finding appended data, scripting is more efficient and less error-prone than manual inspection.
-   **Binary File Handling**: When dealing with file formats, it's crucial to read them in binary mode to avoid data corruption from text encoding assumptions.
-   **File Format Markers**: Understanding key markers in file formats (like `%%EOF` in PDF) is fundamental for programmatic analysis and data carving.
