Music Leak — Sebastien Taupiac - OSINT CTF Writeup
Challenge Overview
Challenge Name: Music Leak — Sebastien Taupiac
Category: OSINT
Description:
An email address — sebastien.taupiac@orange.fr — turned up while doing threat-intel on leaked credentials related to music streaming services. Rumor has it that Sebastien’s account password was shared on a public list and also appears in archives and posts on underground forums and paste sites.
Your job is to locate the leaked password for that email by searching public and archival sources (and any mirrored copies). Treat this as an OSINT exercise: confirm the leak by correlating the same password string across multiple sources, check any contextual metadata, and identify the most credible leak instance.
Flag Format: ctf7{...}
Solution Walkthrough
Step 1: Identify the Target and Initial Search

The email sebastien.taupiac@orange.fr is the primary identifier, linked to music streaming services like Spotify.
Begin with credential leak searches: Query breach databases (e.g., Have I Been Pwned?) and web searches for: "sebastien.taupiac@orange.fr" password or "sebastien.taupiac@orange.fr" spotify leak.
Focus on paste sites, forums, and archives, as the challenge emphasizes underground sharing and mirrored copies.

Step 2: Locate the Leaked Lists

Searches reveal multiple public paste dumps of Spotify premium accounts. Key hits include mirrors on pastebin.pl (e.g., views like 86b5e2e7 and 8b3c631d), which are archival copies of credential lists from underground forums.
These pastes are formatted as email-password pairs, often from 2023-2024 Spotify breaches or shares. Cross-reference with Wayback Machine or similar for archived versions to confirm persistence.

Step 3: Extract and Correlate the Credentials

In the pastes, locate sebastien.taupiac@orange.fr paired with the password Topiseb77.
Correlate across sources: The exact same password appears in at least two independent pastes (e.g., pastebin.pl/view/86b5e2e7 and pastebin.pl/view/8b3c631d), with metadata indicating Spotify context (e.g., "Spotify Premium Accounts 2024" titles).
No variations found; this string is consistent, ruling out false positives. The password's structure ("Topi" possibly a name variant, "seb" for Sebastien, "77" as birth year or pattern) adds credibility.

Step 4: Verify Credibility and Context

Check for metadata: Pastes include timestamps around mid-2024, aligning with known music streaming leaks. No conflicting passwords in other sources.
Underground forum mentions (via search snippets) point to the same dump, confirming it's not isolated. The music streaming tie-in directly matches the challenge's threat-intel angle.

Flag
ctf7{Topiseb77}
Tools Used

Web search engines (Google/Bing with operators like filetype:paste)
Paste site viewers (Pastebin.pl, etc.)
Breach checkers (Have I Been Pwned? for initial hits)
Archive tools (Wayback Machine for mirrors)

Lessons Learned

Mirrored pastes are common in OSINT—always cross-verify across multiples to avoid one-off fakes.
Email leaks in niche sectors (e.g., streaming) often cluster; targeted queries yield faster results.
Password patterns like this (personalized with numbers) are easy to spot but highlight reuse risks—use managers!

Solved on: November 09, 2025
