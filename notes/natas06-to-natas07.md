# Natas 6 → Natas 7

## Level Information

* URL: `http://natas6.natas.labs.overthewire.org`
* Username: `natas6`
* Password: `[REDACTED]`

## Goal

Find the password for the next level, `natas7`.

## Page Observation

When I opened the page, I saw an input field:

```text
Input secret:
```

There was also a link:

```text
View sourcecode
```

This means the level expects a secret value, and the source code may reveal how the secret is checked.

## Concept

This level introduces source code review and exposed include files.

The page source code includes another PHP file:

```php
include "includes/secret.inc";
```

This means the secret value is stored in a separate file named:

```text
includes/secret.inc
```

If this file is publicly accessible, I can open it directly in the browser and read the secret.

## Steps

1. Opened the Natas 6 page.
2. Clicked `View sourcecode`.
3. Reviewed the PHP source code.
4. Found that the page includes this file:

```text
includes/secret.inc
```

5. Opened the included file directly:

```text
http://natas6.natas.labs.overthewire.org/includes/secret.inc
```

6. Found the secret value inside the file.
7. Copied the secret value.
8. Went back to the main Natas 6 page.
9. Pasted the secret into the input field.
10. Clicked `Submit`.
11. The page displayed the password for `natas7`.

## Important Mistake I Made

At first, I opened the wrong URL:

```text
http://natas6.natas.labs.overthewire.org/index-source.html/includes/secret.inc
```

This caused a `404 Not Found` error because `index-source.html` is a file, not a directory.

The correct URL was:

```text
http://natas6.natas.labs.overthewire.org/includes/secret.inc
```

## Browser Method

Open the exposed include file:

```text
http://natas6.natas.labs.overthewire.org/includes/secret.inc
```

Copy the secret value, then submit it on:

```text
http://natas6.natas.labs.overthewire.org/
```

## Terminal Method

Get the secret file:

```bash
curl -u natas6:[REDACTED] \
http://natas6.natas.labs.overthewire.org/includes/secret.inc
```

Submit the secret:

```bash
curl -u natas6:[REDACTED] \
-d "secret=[REDACTED_SECRET]&submit=Submit" \
http://natas6.natas.labs.overthewire.org/
```

## Result

After submitting the correct secret value, the page displayed the password for `natas7`.

## Lesson Learned

Sensitive include files should not be publicly accessible from the web root. Even if a file is intended to be used internally by PHP, users may still access it directly if the server exposes it.

Secrets should be stored outside the public web directory or protected with proper server-side access control.

## Password

```text
[REDACTED]
```
