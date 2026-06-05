# Natas 8 → Natas 9

## Level Information

* URL: `http://natas8.natas.labs.overthewire.org`
* Username: `natas8`
* Password: `[REDACTED]`

## Goal

Find the password for the next level, `natas9`.

## Page Observation

When I opened the page, I saw an input field:

```text
Input secret:
```

There was also a link:

```text
View sourcecode
```

This means the page expects a secret value, and the source code may show how the secret is checked.

## Concept

This level introduces encoding and reverse engineering simple transformations.

The source code contains an encoded secret and a function that encodes the user input before comparing it with the stored value.

The encoding process is:

```text
secret → base64_encode → strrev → bin2hex
```

To recover the original secret, I need to reverse the process:

```text
hex2bin → strrev → base64_decode
```

This shows that encoding is not the same as encryption. If the encoded value and the algorithm are visible, the original value can be recovered.

## Source Code Review

After clicking `View sourcecode`, I found an encoded secret:

```php
$encodedSecret = "3d3d516343746d4d6d6c315669563362";
```

The source code also showed this function:

```php
function encodeSecret($secret) {
    return bin2hex(strrev(base64_encode($secret)));
}
```

This means the submitted secret is encoded and compared with the stored encoded value.

## Steps

1. Opened the Natas 8 page.
2. Clicked `View sourcecode`.
3. Found the encoded secret value.
4. Reviewed the `encodeSecret()` function.
5. Identified the encoding order:

```text
base64_encode → reverse string → convert to hexadecimal
```

6. Reversed the encoding order:

```text
convert from hexadecimal → reverse string → base64 decode
```

7. Decoded the secret.
8. Submitted the decoded secret into the input field.
9. The page displayed the password for `natas9`.

## Browser Console Method

I opened Developer Tools using:

```text
F12
```

Then I went to the `Console` tab and used this JavaScript code:

```js
atob(
  "3d3d516343746d4d6d6c315669563362"
    .match(/.{2}/g)
    .map(hex => String.fromCharCode(parseInt(hex, 16)))
    .join("")
    .split("")
    .reverse()
    .join("")
)
```

The output was the decoded secret.

## Terminal Method

Using PHP:

```bash
php -r 'echo base64_decode(strrev(hex2bin("3d3d516343746d4d6d6c315669563362")));'
```

## Submit the Secret

After decoding the secret, I submitted it on:

```text
http://natas8.natas.labs.overthewire.org/
```

## Result

After submitting the correct decoded secret, the page displayed the password for `natas9`.

## Lesson Learned

Encoding should not be used as a security mechanism. Encoding only transforms data into another format, but it does not protect the data if the algorithm is known.

Sensitive secrets should not be hardcoded in source code or protected only by reversible encoding.

## Password

```text
[REDACTED]
```
