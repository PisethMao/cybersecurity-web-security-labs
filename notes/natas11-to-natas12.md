# Natas 11 → Natas 12

## Level Information

* URL: `http://natas11.natas.labs.overthewire.org`
* Username: `natas11`
* Password: `[REDACTED]`

## Goal

Find the password for the next level, `natas12`.

## Page Observation

When I opened the page, I saw this message:

```text
Cookies are protected with XOR encryption
```

There was also a background color input:

```text
Background color: #ffffff
```

and a link:

```text
View sourcecode
```

This means the website stores some data inside a cookie and protects it using XOR encryption.

## Concept

This level introduces weak cookie encryption using XOR.

XOR encryption can be reversible if the attacker knows or can guess the plaintext. In this level, the default cookie data is predictable because the source code shows the default array:

```php
$defaultdata = array("showpassword"=>"no", "bgcolor"=>"#ffffff");
```

The goal is to change:

```json
{"showpassword":"no","bgcolor":"#ffffff"}
```

into:

```json
{"showpassword":"yes","bgcolor":"#ffffff"}
```

If the server accepts the modified encrypted cookie, it will display the password for `natas12`.

## Source Code Review

After clicking `View sourcecode`, I found these important parts:

```php
$defaultdata = array("showpassword"=>"no", "bgcolor"=>"#ffffff");
```

The source code also showed an XOR encryption function:

```php
function xor_encrypt($in) {
    $key = '<censored>';
    $text = $in;
    $outText = '';

    for($i=0;$i<strlen($text);$i++) {
        $outText .= $text[$i] ^ $key[$i % strlen($key)];
    }

    return $outText;
}
```

The cookie is saved like this:

```php
setcookie("data", base64_encode(xor_encrypt(json_encode($d))));
```

This means the cookie value is:

```text
JSON data → XOR encryption → Base64 encoding
```

To modify it, I need to recover the XOR key and create a new valid cookie.

## Vulnerability Explanation

XOR works like this:

```text
plaintext XOR key = ciphertext
```

If I know the plaintext and the ciphertext, I can recover the key:

```text
ciphertext XOR plaintext = key
```

In this level:

* Known plaintext: `{"showpassword":"no","bgcolor":"#ffffff"}`
* Ciphertext: the decoded value of the `data` cookie
* Goal plaintext: `{"showpassword":"yes","bgcolor":"#ffffff"}`

After recovering the key, I can encrypt the new plaintext and replace the cookie value.

## Steps

1. Opened the Natas 11 page.
2. Clicked `View sourcecode`.
3. Found the default data:

```json
{"showpassword":"no","bgcolor":"#ffffff"}
```

4. Opened Chrome Developer Tools using:

```text
F12
```

5. Went to:

```text
Application → Cookies → http://natas11.natas.labs.overthewire.org
```

6. Found the cookie named:

```text
data
```

7. Used JavaScript in the browser console to recover the XOR key.
8. Created a new cookie with:

```json
{"showpassword":"yes","bgcolor":"#ffffff"}
```

9. Replaced the old `data` cookie with the new encrypted cookie.
10. Refreshed the page.
11. The page displayed the password for `natas12`.

## Important Problem I Had

At first, I tried to run a PHP script:

```bash
php natas11-solve.php
```

But my terminal returned:

```text
bash: php: command not found
```

This happened because PHP was not installed on my Windows/Git Bash environment.

Instead of installing PHP, I solved it using JavaScript in the browser console.

## Browser Console Method

Open the Natas 11 page and press:

```text
F12
```

Then go to the `Console` tab and run this JavaScript code:

```js
function xorEncrypt(input, key) {
  let output = "";
  for (let i = 0; i < input.length; i++) {
    output += String.fromCharCode(
      input.charCodeAt(i) ^ key.charCodeAt(i % key.length)
    );
  }
  return output;
}

function findRepeatingKey(stream) {
  for (let len = 1; len <= stream.length; len++) {
    const key = stream.slice(0, len);
    let ok = true;

    for (let i = 0; i < stream.length; i++) {
      if (stream[i] !== key[i % len]) {
        ok = false;
        break;
      }
    }

    if (ok) return key;
  }

  return stream;
}

const cookieMatch = document.cookie.match(/(?:^|;\s*)data=([^;]+)/);

if (!cookieMatch) {
  console.log("data cookie not found");
} else {
  const currentCookie = decodeURIComponent(cookieMatch[1]);

  const knownPlaintext = JSON.stringify({
    showpassword: "no",
    bgcolor: "#ffffff",
  });

  const cipherText = atob(currentCookie);

  const keyStream = xorEncrypt(cipherText, knownPlaintext);
  const key = findRepeatingKey(keyStream);

  console.log("Recovered key:", key);

  const newPlaintext = JSON.stringify({
    showpassword: "yes",
    bgcolor: "#ffffff",
  });

  const newCookie = btoa(xorEncrypt(newPlaintext, key));

  console.log("New cookie:", newCookie);

  document.cookie = "data=" + encodeURIComponent(newCookie) + "; path=/";

  console.log("Cookie updated. Refreshing page...");
  location.reload();
}
```

## Result

After replacing the encrypted cookie with a new valid cookie containing `showpassword=yes`, the page displayed the password for `natas12`.

## Lesson Learned

XOR encryption is weak when the plaintext is predictable. If an attacker knows both the plaintext and ciphertext, they can recover the XOR key.

Sensitive authorization data should not be stored in client-side cookies unless it is properly protected. A server should not trust client-controlled data for access control.

## Password

```text
[REDACTED]
```
