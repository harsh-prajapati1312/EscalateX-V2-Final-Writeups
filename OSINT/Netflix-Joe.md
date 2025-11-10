# Netflix Joe - OSINT CTF Writeup

## Challenge Overview
**Challenge Name:** Netflix Joe  
**Category:** OSINT  
**Description:**  
A user going by the email `cupp_0_joe@hotmail.com` seems to have reused his Netflix password across multiple platforms. Recently, a public PDF document surfaced that appears to contain hints about his password pattern.  

Your task is to find Joe’s leaked password — maybe his obsession with Netflix gave it away? The answer lies in connecting the dots between what he watches and what he shares.  

**Flag Format:** `ctf7{...}`

## Solution Walkthrough

### Step 1: Identify the Target and Initial Search
- The email `cupp_0_joe@hotmail.com` is the key identifier. "CUPP" likely refers to the Common User Passwords Profiler tool, hinting at a generated password pattern.
- Start by searching for credential leaks associated with this email. Use OSINT tools like Have I Been Pwned? or general web searches for breached data.
- Query: `"cupp_0_joe@hotmail.com" password` or similar variations on search engines.

### Step 2: Locate the Leaked PDF
- A public PDF document was mentioned as containing hints. Search for: `"cupp_0_joe@hotmail.com" PDF Netflix`.
- This leads to a Scribd document (ID: 856475871) titled something like "Netflix Premium Accounts List" or similar, which is a leaked credential dump.
- The PDF is also mirrored/shared on paste sites like Throwbin.io.

### Step 3: Extract the Credentials
- Download or view the PDF. It contains a list of Netflix account credentials in a tabular format.
- Scan for `cupp_0_joe@hotmail.com`. The associated password is `r04r08j10`.
- Verification: This password fits a pattern (possibly CUPP-generated, like leetspeak or sequential numbers), tying into the "obsession with Netflix" theme—perhaps referencing shows or dates he watches/shares.

### Step 4: Connect the Dots
- Joe's "sharing" likely refers to the public leak of this PDF, which he or someone else posted online.
- The Netflix tie-in: The password reuse across platforms, with the leak being a Netflix-specific dump, confirms the obsession angle.
- No further cracking needed—the direct leak provides the password.

## Flag
`ctf7{r04r08j10}`

## Tools Used
- Web search engines (Google/Bing)
- Document viewers (Scribd, PDF readers)
- Breach databases (optional for verification)

## Lessons Learned
- Password reuse is dangerous—always use unique, strong passwords and a manager.
- Public pastes and document shares are goldmines for OSINT; always check metadata and contents.
- CUPP patterns are predictable; avoid generator-based passwords if reusing.

*Solved on: November 09, 2025*
