# Natas 2 → Natas 3

## Level Information

- URL: http://natas2.natas.labs.overthewire.org
- Username: natas2
- Password: [REDACTED]

## Goal

Find the password for the next level.

## Concept

This level teaches basic directory enumeration. Even if a webpage says there is nothing visible, the HTML source may reference hidden files or folders. If a public directory is exposed, sensitive files may be accessible directly.

## Steps

1. Opened the Natas 2 page.
2. Viewed the page source using `Ctrl + U`.
3. Found an image path inside the source code.
4. Opened the `/files/` directory.
5. Found `users.txt`.
6. Opened `users.txt` and found the password for `natas3`.

## Commands Used

```bash
curl -u natas2:[REDACTED] http://natas2.natas.labs.overthewire.org

curl -u natas2:[REDACTED] http://natas2.natas.labs.overthewire.org/files/users.txt