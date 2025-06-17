[![MseeP.ai Security Assessment Badge](https://mseep.net/pr/kenhuangus-mcp-vulnerable-server-demo-badge.png)](https://mseep.ai/app/kenhuangus-mcp-vulnerable-server-demo)

# Insecure MCP Demo

## Overview
This project demonstrates a vulnerable MCP server and multiple clients, including a proof-of-concept attack client and also a good client. It is designed for educational purposes to showcase potential security vulnerabilities in MCP server.

## Project Structure
- `vuln-mcp.py`: Vulnerable MCP server exposing insecure tools.
- `good-mcp-client.py`: Regular good client for normal interactions (insert/query records).
- `attack-mcp-client.py`: Automated attack client that demonstrates exploitation of server vulnerabilities.
- `requirements.txt`: Python dependencies for the project.

## Features & Vulnerabilities
### Exposed Server Tools
1. **insert_record**
   - Inserts a name/address record into the database.
   - **Vulnerability:** Prone to SQL injection due to direct string interpolation of user input into SQL queries.
2. **query_records**
   - Lists all records in the database.
   - **Vulnerability:** Exposes all data without authentication or access control.
3. **execute_sql**
   - Executes arbitrary SQL queries provided by the client.
   - **Vulnerability:** Allows any SQL command, including destructive ones (e.g., data exfiltration, schema changes).
4. **get_env_variable**
   - Returns the value of any environment variable requested.
   - **Vulnerability:** Leaks sensitive environment variables (e.g., secrets, API keys).

## How to Run
### 1. Install Dependencies
```bash
pip install -r requirements.txt
```

### 2. Start the Server and Good Client
In one terminal:
```bash
python good-mcp-client.py vuln-mcp.py
```
Follow the prompts to insert/query records interactively.

### 3. Run the Attack Client
In another terminal:
```bash
python attack-mcp-client.py vuln-mcp.py
```
This will automatically:
- Attempt SQL injection attacks
- Execute arbitrary SQL queries
- Attempt to read several common environment variables

## Example Output
- Attack client will show which payloads succeed or fail, and print out database contents and environment variable values if accessible.

## Vulnerabilities Demonstrated
- **SQL Injection:** User input is unsanitized, allowing attackers to manipulate SQL logic and insert arbitrary data.
- **Arbitrary Code Execution:** The `execute_sql` tool allows attackers to run any SQL command, including data theft or destruction.
- **Sensitive Data Exposure:** The `get_env_variable` tool allows attackers to read secrets and configuration values.
- **Lack of Access Control:** Anyone can run all tools and access all data without authentication.

## Mitigation Strategies
To secure a real-world MCP server, you should:

1. **Use Parameterized Queries:**
   - Always use parameter substitution instead of string interpolation for SQL queries to prevent injection.
   - Example (secure):
     ```python
     cursor.execute("INSERT INTO records (name, address) VALUES (?, ?)", (name, address))
     ```
2. **Restrict Dangerous Tools:**
   - Remove or strictly limit tools like `execute_sql` and `get_env_variable`.
   - Only expose necessary functionality.
3. **Implement Authentication & Authorization:**
   - Require users to authenticate and check permissions before allowing access to sensitive tools or data.
4. **Validate and Sanitize Input:**
   - Check and sanitize all user inputs, especially those that interact with the database or system.
5. **Limit Environment Variable Access:**
   - Only allow access to non-sensitive variables, or remove this tool entirely.
6. **Audit and Monitor Usage:**
   - Log all tool invocations and monitor for suspicious or abusive behavior.
7. **Principle of Least Privilege:**
   - Run the server with minimal privileges and restrict database and OS access as much as possible.

## Disclaimer
This project is for educational and demonstration purposes only. **Do not deploy this code in production environments.**

---

For questions or further improvements, please open an issue or contact the project maintainer.
