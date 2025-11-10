# The Leaker — Sebastien Part 2 - OSINT CTF Writeup

## Challenge Overview
**Challenge Name:** The Leaker — Sebastien Part 2  
**Category:** OSINT  
**Description:**  
SOLVE MUSIC LEAK TO SOLVE THIS  
Following the Music Leak — Sebastien Taupiac challenge, investigators discovered that the leaked password entry was uploaded to a public paste/archive site 8 years after the original dump was created. The upload included a short comment and a link to other dumps — and the uploader left a consistent online handle across several posts.  

Your mission: find the uploader’s public handle on social media (the person who uploaded the leaked password for sebastien.taupiac@orange.fr) and submit it as the flag.  

**Flag Format:** `ctf7{...}`

## Solution Walkthrough

### Step 1: Reference the Previous Challenge
- From "Music Leak — Sebastien Taupiac", the key credential is `sebastien.taupiac@orange.fr:Topiseb77` from Spotify premium dumps.
- Focus on the original dump's metadata: Search for the paste containing this entry, noting creation dates and upload history.
- Query: `"sebastien.taupiac@orange.fr" Topiseb77 spotify paste` or similar to trace origins.

### Step 2: Locate the Original Dump and Timeline
- The primary archive is on Pastebin.pl (e.g., view/f8ba7c8c), created ~8 years ago (around 2017).
- Check for mirrors and re-uploads: Recent 2024-2025 pastes reference this original, but the challenge hints at a delayed upload with comments/links.
- Use archive tools like Wayback Machine to inspect snapshots: Search for the paste URL and look for uploader notes or credits.

### Step 3: Identify the Uploader's Handle
- In the original paste's metadata and credits (often at the top/bottom), the uploader is attributed to Twitter user `@realLuschi`.
- Correlate: Search for `@realLuschi spotify leak` or `@realLuschi pastebin`—results show consistent posting of credential dumps across forums, pastes, and social media.
- Verify consistency: The handle appears in multiple leak-sharing contexts (e.g., archived Twitter posts from 2017 linking to dumps), with no conflicts. Recent mirrors credit the same source without new handles.

### Step 4: Confirm Social Media Presence
- X/Twitter search for `@realLuschi`: Profile matches leak-sharing patterns, with posts about underground dumps and links to pastes.
- No other handles fit the "consistent online handle" description; this ties directly to the upload of the Sebastien entry.

## Flag
`ctf7{realLuschi}`

## Tools Used
- Web search engines (Google/Bing with date filters)
- Paste site viewers (Pastebin.pl archives)
- Archive tools (Wayback Machine for historical snapshots)
- X/Twitter search (for handle verification)

## Lessons Learned
- Chain challenges together—previous solutions provide crucial breadcrumbs like exact credentials for tracing origins.
- Metadata in pastes (credits, comments) often reveals actors; always inspect headers/footers.
- Social media handles in underground scenes are persistent identifiers—cross-platform searches solidify attributions.
- Ethical note: OSINT on leaks highlights real privacy risks; report active threats responsibly.

*Solved on: November 09, 2025*
