# Natas 3 → Natas 4

## Level Information

* URL: `http://natas3.natas.labs.overthewire.org`
* Username: `natas3`
* Password: `[REDACTED]`

## Goal

Find the password for the next level, `natas4`.

## Page Observation

When I opened the page, it displayed the message:

```text
There is nothing on this page
```

At first, the page looked empty. However, in web security, an empty page does not always mean there is no useful information. Hidden files, comments, or directories may still exist on the server.

## Concept

This level introduces the use of `robots.txt`.

A `robots.txt` file is used by websites to tell search engines which paths should not be indexed. However, this file is publicly accessible, so it can also reveal hidden directories to users or attackers.

This means `robots.txt` should not be used as a security control.

## Steps

1. Opened the Natas 3 page.
2. Viewed the page source using:

```text
Ctrl + U
```

3. Found a hint related to search engines.
4. Opened the `robots.txt` file:

```text
http://natas3.natas.labs.overthewire.org/robots.txt
```

5. Found a disallowed directory in `robots.txt`.
6. Opened the hidden directory.
7. Found a `users.txt` file.
8. Opened `users.txt`.
9. Found the password for `natas4`.

## URLs Checked

```text
http://natas3.natas.labs.overthewire.org/robots.txt
```

```text
http://natas3.natas.labs.overthewire.org/[hidden-directory]/users.txt
```

## Commands Used

```bash
curl -u natas3:[REDACTED] http://natas3.natas.labs.overthewire.org/
```

```bash
curl -u natas3:[REDACTED] http://natas3.natas.labs.overthewire.org/robots.txt
```

```bash
curl -u natas3:[REDACTED] http://natas3.natas.labs.overthewire.org/[hidden-directory]/users.txt
```

## Mistake I Made

At first, I tried to open `/some-directory`, but that was only a placeholder example. The correct method was to check `/robots.txt` first and use the real disallowed directory shown there.

## Result

The password for `natas4` was found inside a public file discovered through the directory listed in `robots.txt`.

## Lesson Learned

`robots.txt` is not a security mechanism. It only gives instructions to search engines. Since users can read it directly, sensitive directories and files should not be exposed there.

Proper server-side access control must be used to protect sensitive files.

## Password

```text
[REDACTED]
```
