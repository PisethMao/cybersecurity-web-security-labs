# Natas 5 → Natas 6

## Level Information

* URL: `http://natas5.natas.labs.overthewire.org`
* Username: `natas5`
* Password: `[REDACTED]`

## Goal

Find the password for the next level, `natas6`.

## Page Observation

When I opened the page, it displayed this message:

```text
Access disallowed. You are not logged in
```

This means the website thinks my current session is not authenticated.

## Concept

This level introduces client-side cookies.

A cookie is a small piece of data stored in the browser and sent to the server with each request. In this level, the website uses a cookie named `loggedin` to decide whether the user is logged in or not.

The original cookie value was:

```text
loggedin = 0
```

This means the user is not logged in.

By changing the value to:

```text
loggedin = 1
```

the server treated the user as logged in and displayed the password for the next level.

## Steps

1. Opened the Natas 5 page.
2. Saw the message saying I was not logged in.
3. Opened Chrome Developer Tools using:

```text
F12
```

4. Went to:

```text
Application → Storage → Cookies → http://natas5.natas.labs.overthewire.org
```

5. Found the cookie named:

```text
loggedin
```

6. Changed its value from:

```text
0
```

to:

```text
1
```

7. Refreshed the page.
8. The page displayed the password for `natas6`.

## Browser Method

Cookie before modification:

```text
loggedin = 0
```

Cookie after modification:

```text
loggedin = 1
```

Then refresh:

```text
http://natas5.natas.labs.overthewire.org/
```

## Terminal Method

```bash
curl -u natas5:[REDACTED] \
--cookie "loggedin=1" \
http://natas5.natas.labs.overthewire.org/
```

## Result

After changing the `loggedin` cookie from `0` to `1`, the server displayed the password for `natas6`.

## Lesson Learned

Client-side cookies can be modified by users. A website should not trust a simple client-controlled value such as `loggedin=1` for authentication.

Authentication and authorization must be validated securely on the server side.

## Password

```text
[REDACTED]
```
