

## Introduction
SQL Injection (SQLi) is a critical security vulnerability that occurs when an attacker can interfere with the queries that an application makes to its database. It typically allows an attacker to bypass authentication, view unauthorized data, or even modify/delete database records.

## Mechanism: Vulnerable String Concatenation
The vulnerability arises when user-provided input is directly concatenated into a SQL query string without proper sanitization.

**Vulnerable Example (Simulated in this lab):**
```javascript
const query = "SELECT * FROM users WHERE username = '" + username + "' AND password = '" + password + "'";
```
In this case, the database treats the entire resulting string as a single command, allowing malicious input to alter the logic of the query.

## Common Attack Techniques

### 1. Tautology-Based Attacks
Attackers use conditional logic that always evaluates to true to bypass checks.
- **Payload:** `' OR '1'='1`
- **Resulting Query:** `SELECT * FROM users WHERE username = 'admin' AND password = '' OR '1'='1'`
- **Effect:** The `OR '1'='1'` condition makes the entire `WHERE` clause true for every row, often logging the attacker in as the first user in the database (typically the administrator).

### 2. Comment-Based Attacks
Attackers use SQL comment characters (`--`) to truncate the rest of the query, removing the secondary check (like a password).
- **Payload:** `' OR '1'='1' --`
- **Resulting Query:** `SELECT * FROM users WHERE username = 'admin' AND password = '' OR '1'='1' --'`
- **Effect:** Everything after the `--` is ignored, effectively removing the password requirement.

## Countermeasures

### 1. Parameterized Queries (Prepared Statements)
This is the primary defense against SQL injection. Instead of building a query string with user input, the application defines the SQL structure first and sends the user input as separate parameters.
- **Mechanism:** The database engine compiles the SQL command before the user data is attached.
- **Effect:** User inputs are treated strictly as **literal data** and never as executable SQL code. A payload like `' OR '1'='1` would be searched for as a literal password string, failing to find a match.

### 2. Input Validation
Validating that input matches expected formats (e.g., email, numeric ID) helps reduce the attack surface, though it should be used in conjunction with parameterized queries.

## Simulation Correlation
This experiment demonstrates these concepts through two modes:
- **Vulnerable Mode**: Shows how direct concatenation lead to successful authentication bypass using the payloads discussed above.
- **Secure Mode**: Shows how parameterized queries (prepared statements) protect the application by treating the same payloads as harmless data.