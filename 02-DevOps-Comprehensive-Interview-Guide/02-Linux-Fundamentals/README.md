# Section 2: Linux Theory and Practical Questions

---

## Q9. What is Linux, and why is it important in DevOps?

**🔑 Keywords:** Linux kernel, distribution, server operating system, container runtime, CLI management, systemd

**Beginner-Friendly Answer:**
Linux is the foundational operating system of the modern cloud. Containers, Kubernetes worker nodes, CI build agents, and cloud VMs almost universally run on Linux. Knowing how to navigate the command line, manage services, read logs, and configure networking in Linux is an indispensable skill for any DevOps engineer.

**Detailed Answer:**
Linux is an operating system widely used for servers, containers, cloud workloads, and DevOps infrastructure. Many CI agents, container images, Kubernetes nodes, and cloud instances use Linux.

### Cross-question: What is the difference between Linux and a Linux distribution?

**Answer:**
Linux technically refers to the kernel. A distribution combines the kernel with system utilities, package management, and other software. Ubuntu and Amazon Linux are examples of distributions.

---

## Q10. What is a process?

**🔑 Keywords:** process ID (PID), program execution, address space, resource usage, `ps aux`, `top`, system monitoring

**Beginner-Friendly Answer:**
A program is just a file stored on disk (like a recipe). When you execute that program, the operating system loads it into memory and runs it — that active, running instance is called a **process**. Each process receives its own unique ID (PID) and assigned CPU and RAM resources.

**Detailed Answer:**
A process is a running instance of a program. Each process has information such as a process ID (PID), execution state, and resource usage.

### Follow-up: How do you find running processes?

```bash
ps aux
top
```

`ps aux` lists processes, while `top` provides a continuously updated view of resource usage.

---

## Q11. What is the difference between a process and a thread?

**🔑 Keywords:** memory isolation, shared address space, multithreading, container process lifecycle, PID 1

**Beginner-Friendly Answer:**
Think of a **process** as a whole house with its own rooms and private backyard (its own memory space). A **thread** is an individual person living inside that house: multiple people (threads) can perform different chores simultaneously while sharing the same kitchen, electricity, and living room (memory). Inside a Docker container, if the main process (PID 1) exits, all its threads stop and the container terminates.

**Detailed Answer:**
A process has its own process-level resources and address space. Threads are execution units within a process that generally share the process's memory and resources.

### Cross-question: Why does this matter in a container?

**Answer:**
A container can run one or more processes and threads. If its main process exits, the container may stop, depending on the container's process and runtime configuration.

---

## Q12. What are file permissions in Linux?

**🔑 Keywords:** owner, group, others, read (4), write (2), execute (1), `chmod`, `chown`, security risks of `777`

**Beginner-Friendly Answer:**
Linux protects files using permissions split into three categories: Owner, Group, and Others. Each category can have Read (4), Write (2), and Execute (1) permissions. For example, `chmod 755 script.sh` means the owner can do everything (4+2+1=7), while everyone else can only read and execute (4+1=5). Never use `chmod 777` because it lets literally anyone modify or delete your files!

**Detailed Answer:**
Linux permissions determine who can read, write, or execute a file. The main permission categories are owner, group, and others.

Example:

```bash
ls -l
chmod 755 script.sh
```

755 gives the owner read, write, and execute permissions, while the group and others receive read and execute permissions.

### Cross-question: Why shouldn't you use chmod 777 everywhere?

**Answer:**
It grants everyone read, write, and execute permissions. That can expose files to unauthorized modification and create security risks.

---

## Q13. What is the difference between sudo and su?

**🔑 Keywords:** `sudo` (command elevation), `su` (switch user), root privileges, audit logging

**Beginner-Friendly Answer:**
`sudo` is like borrowing a manager's stamp for just one specific task — you run a single command with root privileges, and your own identity is logged in system audit logs. `su` (switch user) is like putting on a disguise and completely becoming the manager (root) until you log out. Using `sudo` is safer and more traceable.

**Detailed Answer:**
`sudo` runs a permitted command with elevated privileges. `su` switches to another user account, commonly the root account, subject to authentication and system configuration.

---

## Q14. What are environment variables?

**🔑 Keywords:** `export`, runtime configuration, process scope, secrets management, non-hardcoded configuration

**Beginner-Friendly Answer:**
Environment variables are dynamic values passed to running programs from outside the code. Instead of hardcoding `PORT = 8080` inside your source files, you read `process.env.PORT`. This lets the same application run on your laptop, a staging server, or AWS simply by changing the environment variables.

**Detailed Answer:**
Environment variables provide configuration to processes without requiring every setting to be hardcoded in the application.

Example:

```bash
export APP_ENV=production
echo "$APP_ENV"
```

### Cross-question: Should you store passwords directly in environment variables?

**Answer:**
They can be used to supply secrets to a process, but sensitive values need careful handling because they may be exposed through logs, process inspection, or misconfigured tooling. Production systems should use an appropriate secrets-management solution.

---

## Q15. What is the difference between grep, find, and tail?

**🔑 Keywords:** `grep` (text pattern search), `find` (file/directory search), `tail -f` (stream live logs), troubleshooting toolkit

**Beginner-Friendly Answer:**
- **`find`** searches your hard drive for *files and folders* by name, size, or date (e.g., "Find all `.log` files").
- **`grep`** searches *inside* file text for matching words or regular expressions (e.g., "Find all lines containing `ERROR`").
- **`tail -f`** watches the bottom of a file in real-time as new log lines arrive.

**Detailed Answer:**
- `grep` searches text for matching patterns.
- `find` searches for files and directories.
- `tail` displays the end of a file.

Examples:

```bash
grep -i "error" app.log
find /var/log -name "*.log"
tail -f app.log
```

### Cross-question: How would you troubleshoot an application that suddenly fails?

**Answer:**
I would inspect the service status and logs, look for recent errors, check resource usage, verify configuration and connectivity, and identify what changed before the failure.

---

## Q16. What is a port?

**🔑 Keywords:** logical communication endpoint, TCP/UDP, listening sockets, firewall rules, service accessibility

**Beginner-Friendly Answer:**
An IP address is the address of a building, and a port is a specific room or door inside that building. Web traffic knocks on port 80 or 443; SSH knocks on port 22; PostgreSQL knocks on port 5432. Just because a port is configured in software doesn't mean users can reach it — cloud firewalls (Security Groups) and network routing must also allow the connection through.

**Detailed Answer:**
A port is a logical communication endpoint used by network services. For example, HTTP commonly uses port 80 and HTTPS commonly uses port 443.

### Cross-question: Does opening a port on a server guarantee that the application is accessible?

**Answer:**
No. The application must be listening on the correct interface and port, and the network path, firewall rules, routing, and security policies must permit the connection.
