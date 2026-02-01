
# 📘 Chapter 5 — Core `docker run` Flags (WHY Each Exists)

> 🧠 Flags are not decorations.  
> Every Docker flag exists because **something broke without it**.

This chapter teaches you to use flags **intentionally**, not habitually.

---

## ❓ Why This Chapter Exists

Most Docker usage looks like this:

```bash
docker run -d -p 80:80 --name app myimage
````

People know *what* to type, but not:

* why these flags exist
* what problem each flag solves
* what breaks if they are misused
* which flags affect **you** vs **the process**

This chapter fixes that.

---

## 🧠 Core Mental Model (Carry Forward)

> **Docker flags control process execution context.**

They do NOT:

* change application logic
* magically add reliability
* fix bad process behavior

Flags shape **how a process runs**, not *what* it is.

---

# 🔧 Flags Covered in This Chapter

We will cover **only flags you actually use daily**:

* `-d` → detached mode
* `-it` → interactive terminal
* `--name` → naming
* `--rm` → auto cleanup
* `-p` → port publishing
* `-e` → environment variables
* `--restart` → restart policies

Each flag is defined **before experiments**.

---

## 🧷 `-d` — Detached Mode

### What it is

Detaches your terminal from the container process.

```bash
docker run -d IMAGE
```

### Why it exists

* Allows background execution
* Frees your shell
* Enables long-running services

### What it does NOT do

* ❌ Does NOT keep containers alive
* ❌ Does NOT daemonize processes
* ❌ Does NOT restart containers

📌 **Rule**

> `-d` affects **your terminal**, not container lifecycle.

---

### 🧪 Experiment

```bash
docker run -d busybox echo "hi"
docker ps
docker ps -a
```

Container exits immediately.

📌

> Detached ≠ persistent.

---

## 🧷 `-it` — Interactive Terminal

### What it is

Combination of:

* `-i` → keep STDIN open
* `-t` → allocate pseudo-TTY

```bash
docker run -it IMAGE
```

### Why it exists

* Enables shells
* Enables REPLs
* Makes programs behave like terminals

### What it does NOT do

* ❌ Does NOT make containers long-lived
* ❌ Does NOT isolate better

📌

> Use `-it` only when a human is interacting.

---

### 🧪 Experiment

```bash
docker run -it busybox sh
ps
exit
```

Container stops when shell exits.

---

## 🧷 `--name` — Human-Readable Naming

### What it is

Assigns a fixed name to a container.

```bash
docker run --name my-app IMAGE
```

### Why it exists

* Random names are not memorable
* Scripts and humans prefer stable identifiers

### Rules

* Names must be unique
* Stopped containers still reserve names

📌

> Names are for humans. IDs are for Docker.

---

### 🧪 Experiment

```bash
docker run --name demo busybox echo ok
docker run --name demo busybox echo fail
```

Error occurs.

Fix:

```bash
docker rm demo
```

---

## 🧷 `--rm` — Automatic Cleanup

### What it is

Deletes container automatically after exit.

```bash
docker run --rm IMAGE
```

### Why it exists

* Prevents container clutter
* Ideal for experiments and tests

### Trade-off

* ❌ No post-mortem debugging
* ❌ Logs are gone

📌

> `--rm` trades debuggability for cleanliness.

---

### 🧪 Experiment

```bash
docker run --rm busybox echo temp
docker ps -a
```

Container is gone.

---

## 🧷 `-p` — Port Publishing (Critical Flag)

### What it is

Maps a **host port** to a **container port**.

```bash
docker run -p HOST_PORT:CONTAINER_PORT IMAGE
```

Example:

```bash
docker run -p 8080:80 nginx
```

### Why it exists

Containers:

* have isolated network namespaces
* are NOT reachable from host by default

`-p` creates a **controlled hole**.

---

### What it does NOT do

* ❌ Does NOT open firewall automatically
* ❌ Does NOT expose all ports
* ❌ Does NOT change app config

📌

> Containers are isolated until you publish ports.

---

### 🧪 Experiment

```bash
docker run -d -p 8080:80 nginx
curl http://localhost:8080
```

Stop container and run without `-p`:

```bash
docker run -d nginx
curl http://localhost:80
```

Fails.

---

## 🧷 `-e` — Environment Variables

### What it is

Passes key-value pairs into container environment.

```bash
docker run -e KEY=value IMAGE
```

### Why it exists

* Externalizes configuration
* Avoids hard-coding
* Enables reuse of images

---

### 🧪 Experiment

```bash
docker run -e MY_NAME=Samuel busybox env
```

Observe output.

📌

> Environment variables configure processes, not containers.

---

## 🧷 `--restart` — Restart Policies

### What it is

Defines what Docker should do **when a container stops**.

```bash
docker run --restart POLICY IMAGE
```

### Policies

| Policy           | Behavior                        |
| ---------------- | ------------------------------- |
| `no`             | Never restart (default)         |
| `always`         | Restart no matter what          |
| `unless-stopped` | Restart unless manually stopped |
| `on-failure`     | Restart only on non-zero exit   |

---

### Why it exists

* Containers fail
* Processes crash
* Docker must know what to do next

📌

> Restart policies express **intent**, not health.

---

### 🧪 Experiment

```bash
docker run -d --restart=always busybox sh -c "sleep 5; exit 1"
docker ps -a
```

Container keeps restarting.

---

## 🚫 Common Flag Misuse (Very Important)

❌ Using `-d` instead of `--restart`
❌ Using `--rm` for long-running services
❌ Exposing ports without understanding scope
❌ Hard-coding config instead of `-e`

Flags don’t fix architecture mistakes.

---

## 🧠 What You Now Understand

* Flags control execution context
* Containers are processes
* Persistence comes from process design + restart policy
* Networking is explicit
* Cleanup is intentional

You are now using Docker **deliberately**.

---

## ➡️ Next Chapter

👉 **Chapter 6 — Inspecting Reality: `ps`, `logs`, `inspect`, `stats`**

Next, we stop guessing and start **observing real state**:

* what’s running
* what failed
* why it failed
* what the kernel is doing


