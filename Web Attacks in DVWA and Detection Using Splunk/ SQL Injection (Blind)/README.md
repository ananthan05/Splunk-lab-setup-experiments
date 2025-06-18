##  Launching an Attack from Another PC in the LAN:

The Medium level uses a form of SQL injection protection with the function mysql_real_escape_string(). However, due to the SQL query not having quotes around the parameter, this does not fully protect the query from being altered. The text box has been replaced with a pre-defined dropdown list and uses POST to submit the form (see source code).

![image](https://github.com/user-attachments/assets/ba981869-abd4-4b40-9554-9be73e9f1330)

 This code tries to protect against SQL injection using `mysqli_real_escape_string()`. This function escapes special characters in the user input.

But there’s a mistake in the SQL query:

```sql
$query = "SELECT first_name, last_name FROM users WHERE user_id = $id;";
```
❌ The user input $id is not inside quotes — so attackers can still inject SQL if they use numbers or logical conditions.

so manyone can injecting something like:

```
1 AND SLEEP(5)
```
That becomes:

```sql
SELECT first_name, last_name FROM users WHERE user_id = 1 AND SLEEP(5);
This is a time-based blind SQL injection, and it works even though you escaped the input.
```
