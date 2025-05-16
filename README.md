# os-command-injection-
**OS Command Injection** is a critical web application vulnerability that allows an attacker to execute arbitrary operating system commands on a server. This can lead to full system compromise depending on the privileges of the application.

---

###  **What Is OS Command Injection?**

OS Command Injection occurs when an application passes unsafe user-supplied input directly to a system shell or command interpreter (`bash`, `sh`, `cmd.exe`, etc.) without proper validation or sanitization.

---

###  **Example: Vulnerable Code**

```php
<?php
// Get IP from user input
$ip = $_GET['ip'];

// Unsafe execution
echo shell_exec("ping -c 4 " . $ip);
?>
```

**Payload:**

```
http://example.com/ping.php?ip=127.0.0.1;id
```

**Result:**
Executes `ping -c 4 127.0.0.1;id`, which also runs the `id` command.

---

###  **Impact**

* Remote Code Execution (RCE)
* File read/write/delete
* Lateral movement within the server
* Full system takeover
  
---

###  **Detection**

* Fuzzing for command separators: `;`, `&&`, `||`, `|`, `&`, newline
* Using automated tools:

  * **Burp Suite**
  * **OWASP ZAP**
  * **Nuclei** (template: `os-command-injection.yaml`)
  * **Commix** (automated OS command injection tool)

---


###  **Common Types of OS Command Injection**

| Type                                        | Description                                           | Example Payload                        |                                            |               |   |          |
| ------------------------------------------- | ----------------------------------------------------- | -------------------------------------- | ------------------------------------------ | ------------- | - | -------- |
| **1. Using Command Separators (`;`)**       | Executes multiple commands sequentially               | `127.0.0.1; whoami`                    |                                            |               |   |         |
| **2. Using Logical AND (`&&`)**             | Executes second command only if the first succeeds    | `127.0.0.1 && whoami`                  |                                            |               |   |         |
| \*\*3. Using Logical OR (\`                 |                                                       | \`)\*\*                                | Executes second command if the first fails | \`invalidhost |   | whoami\` |
| \*\*4. Using Pipe (\`                       | \`)\*\*                                               | Pipes output of one command to another | \`127.0.0.1                                | whoami\`      |   |         |
| **5. Using Subshell Execution (`$()`)**     | Executes command inside parentheses first             | `127.0.0.1$(whoami)`                   |                                            |               |   |         |
| **6. Using Backticks (`` ` ``)**            | Executes command inside backticks                     | `127.0.0.1\`whoami\`\`                 |                                            |               |   |         |
| **7. Newline Injection (`%0a` or `\n`)**    | Inserts a new command on a new line                   | `127.0.0.1%0awhoami`                   |                                            |               |   |         |
| **8. File Injection (via `/proc`)**         | Reads or writes to system files (Linux)               | `; cat /etc/passwd`                    |                                            |               |   |         |
| \*\*9. Command chaining in Windows (`&`, \` | `, `^\`)\*\*                                          | Windows-specific chaining and escaping | `127.0.0.1 & whoami`                       |               |   |         |
| **10. Obfuscated payloads**                 | Bypasses filters (spaces replaced, URL encoded, etc.) | `127.0.0.1;${IFS}id`                   |                                            |               |   |         |

---

###  **Prevention**

* **Never use user input directly in shell commands**
* Use language-native functions instead:
* `exec()`, `system()`, `shell_exec()` should be avoided
* Use **whitelisting** or **strict input validation**
* For system calls, use **safe APIs**:
* Use `escapeshellarg()` and `escapeshellcmd()` in PHP (with caution)
* Use **least privilege** for web application processes

---



