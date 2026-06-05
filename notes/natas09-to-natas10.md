# Natas 9 → Natas 10

## Level Information

* URL: `http://natas9.natas.labs.overthewire.org`
* Username: `natas9`
* Password: `[REDACTED]`

## Goal

Find the password for the next level, `natas10`.

## Page Observation

When I opened the page, I saw a search form:

```text
Find words containing:
```

There was also a link:

```text
View sourcecode
```

The page appeared to search for words from a dictionary file.

## Concept

This level introduces command injection.

Command injection happens when user input is passed directly into an operating system command without proper validation or escaping.

If the application builds a shell command using user input, an attacker may be able to inject extra shell commands.

## Source Code Review

After clicking `View sourcecode`, I found that the application uses a command similar to this:

```php
passthru("grep -i $key dictionary.txt");
```

The user input is inserted directly into the `grep` command.

This is dangerous because shell characters such as `;` can be used to separate commands.

## Vulnerability Explanation

The intended command is:

```bash
grep -i [user_input] dictionary.txt
```

But if the input contains a semicolon, the shell treats it as a command separator.

Example payload:

```text
; cat /etc/natas_webpass/natas10
```

This causes the server to execute another command that reads the password file for `natas10`.

## Steps

1. Opened the Natas 9 page.
2. Clicked `View sourcecode`.
3. Found that user input was passed into a shell command.
4. Identified that the input was vulnerable to command injection.
5. Entered this payload into the search field:

```text
; cat /etc/natas_webpass/natas10
```

6. Clicked `Search`.
7. The page displayed the password for `natas10`.

## Browser Method

Payload used:

```text
; cat /etc/natas_webpass/natas10
```

Submit it in the search input on:

```text
http://natas9.natas.labs.overthewire.org/
```

## Terminal Method

```bash
curl -u natas9:[REDACTED] \
--data-urlencode "needle=; cat /etc/natas_webpass/natas10" \
--data "submit=Search" \
http://natas9.natas.labs.overthewire.org/
```

## Result

After injecting the command, the server executed `cat /etc/natas_webpass/natas10` and displayed the password for `natas10`.

## Lesson Learned

User input must never be inserted directly into shell commands.

To prevent command injection, developers should avoid shell execution when possible. If shell commands are required, they should use strict input validation, allowlists, and safe escaping functions.

## Password

```text
[REDACTED]
```
