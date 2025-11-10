# Writeup: The Vault of the Craftsman

## Introduction

The "Vault of the Craftsman" challenge invites us into the digital legacy of Elias Carpenter, a master craftsman. The story goes that he left behind a digital vault filled with his workshop files, and hidden among them is his "final signature." Our mission is to explore this vault and uncover the secret. The challenge description itself gives a crucial hint: the workshop's preview system can be a bit too revealing if you know how to ask.

**URL:** `https://web2-rz5c.onrender.com`

## First Impressions and Initial Ideas

Upon visiting the website, we're greeted by the "Wood & Wonder Workshop." The main feature is a simple "Design Preview" form with a textarea. This is where users can input a template and see a rendered preview.

The hint in the challenge description, combined with a form that processes user-supplied templates, immediately put me on high alert for **Server-Side Template Injection (SSTI)**. Template engines are powerful, and if they're not configured securely, they can execute code on the server.

## Confirming the Vulnerability: Making the Server Talk

To test my SSTI theory, I started with a basic payload. A simple mathematical operation is a classic and effective way to see if the server is evaluating expressions. I entered:

```jinja
{{ 7 * 7 }}
```

When the preview returned `49`, my suspicion was confirmed. The backend was using a Jinja2 template engine (common in Python/Flask applications) and was processing my input directly. This was my green light to go deeper.

## From Injection to Exploration

With an SSTI vulnerability confirmed, the next step was to move from simple expressions to something more useful, like accessing the server's file system. The challenge was all about exploring a "digital vault," so I needed a way to see what files were on the server.

A common technique for SSTI in Python environments is to traverse the object hierarchy to find powerful modules. I constructed a payload to access the `os` module, which would allow me to run shell commands like `ls`.

This payload climbs up the class tree from a standard object (`config`) to find the global scope where built-in modules are accessible:

```jinja
{{ config.__class__.__init__.__globals__['os'].listdir('.') }}
```

Executing this gave me a list of files and directories in the application's root:

```
['.git', 'static', 'app.py', 'templates', 'runtime.txt', '.venv', 'Procfile.txt', 'requirements.txt', 'vault', '__pycache__']
```

The `vault` directory immediately stood out. This had to be the digital vault mentioned in the story.

## The Journey Through the Vault

Now it was time to explore the `vault`. I modified my payload to list the contents of this new directory:

```jinja
{{ config.__class__.__init__.__globals__['os'].listdir('vault') }}
```

This revealed `['misc', 'note.txt', 'level1']`. The game was afoot.

The path to the final flag was a winding one, designed to test persistence. It involved navigating through a deeply nested set of directories. Along the way, I encountered several `flag.txt` files that contained Base64-encoded strings. These were clever decoys, designed to make you think you'd found the prize.

Here’s a summary of the path I took:

1.  **`/vault`**: Started here.
2.  **`/vault/misc`**: Found the first decoy flag (`super_flag_bits`).
3.  **`/vault/level1`**: Pushed onward.
4.  **`/vault/level1/level2/level3`**: The nesting continued, with another decoy flag (`some_random_base64_flag`) in `level3`.
5.  **`/vault/level1/level2/level3/secret_room/deep`**: Getting closer. Another decoy (`second_flag.plain`) was waiting here.
6.  **`/vault/level1/level2/level3/secret_room/deep/archive`**: This felt like the end of the line.

Inside the `archive` directory, I listed the files one last time:

```jinja
{{ config.__class__.__init__.__globals__['os'].listdir('vault/level1/level2/level3/secret_room/deep/archive') }}
```

This returned `['more', 'helper.doc', 'flag.txt']`. I had a good feeling about this final `flag.txt`.

## The Final Signature

I read the contents of this last file. It contained the Base64 string: `Y3RmN3tzc3RpX2V4cGxvaXRlZF8wMDd9`.

Decoding this was the final step:

```bash
echo "Y3RmN3tzc3RpX2V4cGxvaXRlZF8wMDd9" | base64 -d
```

And there it was, the craftsman's final signature:

**`ctf7{ssti_exploited_007}`**


