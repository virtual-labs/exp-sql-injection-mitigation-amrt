

<h3>Introduction</h3>
SQL Injection (SQLi) is a critical security vulnerability that occurs when an attacker can interfere with the queries that an application makes to its database. It typically allows an attacker to bypass authentication, view unauthorized data, or even modify/delete database records.

<h3>Mechanism: Vulnerable String Concatenation</h3>
The vulnerability arises when user-provided input is directly concatenated into a SQL query string without proper sanitization.

**Vulnerable Example (Simulated in this lab):**
```javascript
const query = "SELECT * FROM users WHERE username = '" + username + "' AND password = '" + password + "'";
```
In this case, the database treats the entire resulting string as a single command, allowing malicious input to alter the logic of the query.

<h3>Common Attack Techniques</h3>

<h4>1. Tautology-Based Attacks</h4>
Attackers use conditional logic that always evaluates to true to bypass checks.
- **Payload:** `' OR '1'='1`
- **Resulting Query:** `SELECT * FROM users WHERE username = 'admin' AND password = '' OR '1'='1'`
- **Effect:** The `OR '1'='1'` condition makes the entire `WHERE` clause true for every row, often logging the attacker in as the first user in the database (typically the administrator).

<h4>2. Comment-Based Attacks</h4>
Attackers use SQL comment characters (`--`) to truncate the rest of the query, removing the secondary check (like a password).
- **Payload:** `' OR '1'='1' --`
- **Resulting Query:** `SELECT * FROM users WHERE username = 'admin' AND password = '' OR '1'='1' --'`
- **Effect:** Everything after the `--` is ignored, effectively removing the password requirement.

<h3>Types of SQL Injection</h3>
SQL Injection attacks can be categorized based on the method used to extract data or interact with the database:

<h4>1. Union-Based SQL Injection</h4>
This technique leverages the `UNION` SQL operator to combine the results of the original query with a malicious query. It allows an attacker to extract data from other tables within the database by appending their results to the legitimate output.

<h4>2. Error-Based SQL Injection</h4>
In this attack, the adversary intentionally provides malformed input to trigger database error messages. These messages often reveal sensitive information about the database's schema, version, or even specific data, which the attacker can use to refine their subsequent attacks.

<h4>3. Blind SQL Injection</h4>
Unlike other types, Blind SQL Injection does not return data directly in the application's response. Instead, the attacker observes the application's behavior (e.g., changes in page content or response time) to infer information. 
- **Boolean-Based:** The application returns different content depending on whether a SQL query evaluates to TRUE or FALSE.
- **Time-Based:** The attacker injects a command that causes the database to wait for a specific amount of time before responding, confirming the success of the injection.

<h3>Countermeasures</h3>

<h4>1. Parameterized Queries (Prepared Statements)</h4>
This is the primary defense against SQL Injection. Instead of building a query string with user input, the application defines the SQL structure first and sends the user input as separate parameters.
- **Mechanism:** The database engine compiles the SQL command before the user data is attached.
- **Effect:** User inputs are treated strictly as **literal data** and never as executable SQL code. A payload like `' OR '1'='1` would be searched for as a literal password string, failing to find a match.

<h4>2. Input Validation</h4>
Validating that input matches expected formats (e.g., email, numeric ID) helps reduce the attack surface, though it should be used in conjunction with parameterized queries.

<h3>Simulation Correlation</h3>
This experiment demonstrates these concepts through two modes:
- **Vulnerable Mode**: Shows how direct concatenation lead to successful authentication bypass using the payloads discussed above.
- **Secure Mode**: Shows how parameterized queries (prepared statements) protect the application by treating the same payloads as harmless data.