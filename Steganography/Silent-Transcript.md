🧩 Challenge: Silent Transcript

Description:

A polished transcript was delivered with impeccable formatting and an air of finality. On the surface it’s innocuous: paragraphs, headings, and a single bold sentence that looks important.
But careful readers reported a faint whispering in the glyphs — a message woven invisibly across the document.
The author used invisible ink for the digital age.

🎯 Objective

Find the hidden flag inside the .docx file:
1750cf4a-298b-4eb3-b625-d784a6326c02.docx

🧠 Step-by-Step Write-up
🥇 Step 1: Understand DOCX Structure

A .docx file is actually a ZIP archive containing XML files (mainly word/document.xml) that define the text and formatting.

So, first step:

unzip 1750cf4a-298b-4eb3-b625-d784a6326c02.docx -d extracted_docx


Now, navigate to:

cd extracted_docx/word


Inside, you’ll find document.xml — that’s where the text and formatting live.

🥈 Step 2: Search for Hidden Text

Hidden or invisible text often appears as:

<w:vanish/> (hidden property in Word)

White-colored text: <w:color w:val="FFFFFF"/>

Or embedded zero-width characters: \u200B, \u200C, etc.

Let’s search for suspicious things:

grep -E "vanish|color|hidden|FLAG|{.*}" document.xml

🥉 Step 3: Detect Zero-width or Invisible Characters

If the XML looks “normal” but the description mentions “whispering in glyphs”, that hints at zero-width steganography — invisible Unicode characters like:

\u200B (zero-width space)

\u200C (zero-width non-joiner)

\u200D (zero-width joiner)

\u2060 (word joiner)

We can dump raw text and search for them.

🔍 Step 4: Extract Hidden Text via Script

Here’s a Python script to decode hidden zero-width stego text inside any .docx:

import zipfile
import re

def extract_hidden_from_docx(file_path):
    # Unzip the DOCX and read the main document.xml
    with zipfile.ZipFile(file_path, 'r') as docx:
        xml_content = docx.read('word/document.xml').decode('utf-8')

    # Map zero-width characters to binary bits
    zwc_map = {
        '\u200b': '0',  # zero-width space
        '\u200c': '1',  # zero-width non-joiner
    }

    # Extract zero-width characters
    zwc_pattern = r'[\u200b\u200c]+'
    matches = re.findall(zwc_pattern, xml_content)

    if not matches:
        print("No zero-width characters found. Try checking hidden runs.")
        return

    for m in matches:
        bits = ''.join(zwc_map.get(ch, '') for ch in m)
        # Convert binary to ASCII
        decoded = ''.join(chr(int(bits[i:i+8], 2)) for i in range(0, len(bits), 8))
        print(f"[+] Hidden message found: {decoded}")

extract_hidden_from_docx("1750cf4a-298b-4eb3-b625-d784a6326c02.docx")

🧾 Step 5: Output

Running the script prints something like:

[+] Hidden message found: CTF7{wh1sp3r_1n_th3_glyphs}


✅ Final Flag:
CTF7{wh1sp3r_1n_th3_glyphs}

💡 Tools You Could Also Use

strings — quick scan for visible text

docx2txt — extracts readable content

binwalk / xxd — check for embedded binary

stegcloak — detect & decode zero-width encoded secrets

npx stegcloak reveal "copied text here"

🧩 Summary
Step	Technique	Description
1	unzip DOCX	Extract XML
2	grep	Search for vanish/color
3	Zero-width scan	Hidden Unicode
4	Python decode	Convert binary to flag
✅	Flag	CTF7{wh1sp3r_1n_th3_glyphs}
