# Writeup: The Eyes of Master Cydo

## An Enigmatic Invitation

The challenge, "The Eyes of Master Cydo," began with a cryptic message. It spoke of a domain that "watches how you look at it" and hinted that the key was to be "polite." This immediately suggested that the solution would involve more than just finding a simple input flaw; it would require understanding the server's own awareness and behavior.

**Target URL:** `http://212.2.248.253:9091`

## First Glance: What the Eyes See

The website itself was minimalist: some ASCII art of eyes and a form field. The page source revealed a `/scan` endpoint that took a `target` parameter. This was the obvious attack surface.

My initial probes were simple. Sending a basic request like `curl -G -d "target=test" http://212.2.248.253:9091/scan` returned a cryptic message: "The flames flicker, but reveal nothing."

However, things got interesting when I tried more direct inputs. Using words like "flag" or "secret" caused the server to hang indefinitely, eventually timing out. This wasn't a crash; it was a deliberate, content-aware defense mechanism. The server was actively filtering my requests based on keywords. This timeout behavior was the first major clue that I was on the right track.

## The Art of Being "Polite"

The challenge description's emphasis on asking "politely" stuck with me. In the context of HTTP requests, politeness often translates to including the right headers to appear like a legitimate user. The most common header for this is `Referer`, which tells the server where the request is coming from.

I decided to craft a request that looked like it originated from the site itself.

```bash
curl -G -d "target=please" -H "Referer: http://212.2.248.253:9091/" http://212.2.248.253:9091/scan
```

Success! With the `Referer` header in place, the timeouts stopped. The server was now willing to engage, believing I was a well-behaved user navigating its own pages.

## Turning the Server's Gaze Inward

The challenge was about the server "watching." This led me to a classic web vulnerability concept: **Server-Side Request Forgery (SSRF)**. What if I could trick the server into "watching" or "scanning" itself?

The `target` parameter was the perfect vehicle for this attack. Instead of providing an external domain, I gave it the loopback IP address, `127.0.0.1`, which always points to the local machine.

This was the payload:

```bash
curl -G -d "target=127.0.0.1" -H "Referer: http://212.2.248.253:9091/" http://212.2.248.253:9091/scan
```

By asking the server to scan itself, I was essentially requesting it to query its own internal environment. The server complied, and in doing so, revealed a secret it was meant to keep hidden.

## The Secret Revealed

The server's response to the SSRF attack was immediate and clear:

```html
<b>The fire reveals the hidden secret:</b><br><span class='flag'>config['SECRET_KEY'] → CTF7{THE_FIRE_REVEALS_THE_MASTER}</span>
```

The flag was exposed. The vulnerability was a textbook SSRF, where the application blindly fetched a URL provided by a user, allowing me to access internal resources.

**Final Flag:** `ctf7{THE_FIRE_REVEALS_THE_MASTER}`

## Reflections and Lessons Learned

"The Eyes of Master Cydo" was a beautifully crafted challenge that highlighted the importance of careful observation and understanding how applications behave.

*   **SSRF is a potent threat:** Functionality that fetches external resources is always a potential vector for SSRF. Developers must assume that users will try to point this functionality inward.
*   **Defense in depth:** The timeout mechanism was a clever distraction and a form of weak protection, but it also served as a giant clue.
*   **Prevention:** The best defense against SSRF is a strict allowlist. Applications should only be permitted to make requests to a known, trusted set of domains and protocols. All other requests should be blocked by default.

This challenge was a great reminder that sometimes, the most elegant hacks involve turning a system's own logic against itself.
