# Natas 4 → Natas 5

## Level Information

* URL: `http://natas4.natas.labs.overthewire.org`
* Username: `natas4`
* Password: `[REDACTED]`

## Goal

Find the password for the next level, `natas5`.

## Page Observation

When I opened the page, it displayed this message:

```text
Access disallowed. You are visiting from "" while authorized users should come only from "http://natas5.natas.labs.overthewire.org/"
```

This means the server checks where the request comes from before allowing access.

## Concept

This level introduces the HTTP `Referer` header.

The `Referer` header tells the server which page the request came from. In this level, the server only allows access if the request appears to come from:

```text
http://natas5.natas.labs.overthewire.org/
```

However, HTTP request headers are controlled by the client, so they can be modified using browser extensions, developer tools, `curl`, Burp Suite, or OWASP ZAP.

## Steps

1. Opened the Natas 4 page.
2. Read the error message.
3. Noticed that the server expected the request to come from `http://natas5.natas.labs.overthewire.org/`.
4. Used a browser extension called ModHeader.
5. Added a custom request header:

```text
Header Name: Referer
Header Value: http://natas5.natas.labs.overthewire.org/
```

6. Refreshed the Natas 4 page.
7. The server accepted the request.
8. Found the password for `natas5`.

## Important Detail

At first, I used this value:

```text
http://natas5.natas.labs.overthewire.org
```

But it did not work because the required value had a final slash:

```text
http://natas5.natas.labs.overthewire.org/
```

The slash matters because the server compares the value strictly.

## Browser Method

Using ModHeader:

```text
Referer: http://natas5.natas.labs.overthewire.org/
```

Then refresh:

```text
http://natas4.natas.labs.overthewire.org/
```

## Terminal Method

```bash
curl -u natas4:[REDACTED] \
-H "Referer: http://natas5.natas.labs.overthewire.org/" \
http://natas4.natas.labs.overthewire.org/
```

## Result

After sending the correct `Referer` header, the page displayed the password for `natas5`.

## Lesson Learned

The `Referer` header should not be trusted for real authorization because users can modify it. Access control must be enforced using secure server-side authentication and authorization, not client-controlled headers.

## Password

```text
[REDACTED]
```
