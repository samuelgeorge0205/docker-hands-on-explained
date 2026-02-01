
# 📘 Chapter 3 — Your First Container (`docker run`) — Explained Line by Line

> 🧠 One command. Many subsystems.  
> We define **everything before we use it**.

---

## ❓ Why This Chapter Exists

Most Docker tutorials jump straight to:

```bash
docker run nginx
````

Without explaining:

* what `run` really does
* what flags mean
* why containers stop
* what Docker assumes for you

This chapter removes **all ambiguity**.

We will:

* define every flag first
* then prove behavior with experiments
* then extract mental rules

No cargo-culting allowed.

---

## 🧠 The Big Idea (Lock This In)

> `docker run` is a **compound command**, not a single action.

Internally, it performs:

1. Image lookup (local)
2. Image pull (if missing)
3. Container creation
4. Namespace & cgroup setup
5. Process execution
6. STDOUT / STDERR wiring
7. Exit code propagation

One command → many systems.

---

## 📐 `docker run` — Full Syntax (Canonical)

```bash
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

### Break it into parts

| Part      | Meaning                      |
| --------- | ---------------------------- |
| `OPTIONS` | How the container should run |
| `IMAGE`   | Filesystem + defaults        |
| `COMMAND` | Process to start             |
| `ARG...`  | Arguments to the process     |

Everything revolves around **starting a process**.

---

# 🔧 Flags You Must Understand (Before Any Experiments)

We will use only these flags in this chapter:

* `-d` → detached mode
* `-it` → interactive terminal
* `--name` → human-readable name
* `--rm` → auto-remove container

Each is defined **now**, before use.

---

## 🧷 `-d` — Detached Mode

### What it is

`-d` tells Docker to **detach your terminal** from the container.

```bash
docker run -d IMAGE
```

### What problem it solves

* Your shell returns immediately
* Container keeps running in background
* You can run other commands

### What it does NOT do

* ❌ Does NOT keep containers alive
* ❌ Does NOT daemonize processes
* ❌ Does NOT affect container lifetime

📌 **Mental rule**

> `-d` affects **your terminal**, not the container process.

---

## 🧷 `-it` — Interactive Terminal

`-it` is actually **two flags combined**.

### `-i` (interactive)

* Keeps STDIN open
* Allows input to the container

### `-t` (tty)

* Allocates a pseudo-terminal
* Makes shells behave normally

```bash
docker run -it IMAGE
```

### When to use it

* Shell access
* REPLs
* Debugging

📌

> Use `-it` only when a human is interacting.

---

## 🧷 `--name` — Container Naming

```bash
docker run --name my-container IMAGE
```

### Why it exists

* Default names are random
* Names help humans
* IDs help Docker

### Rules

* Names must be unique
* Stopped containers still reserve names

📌

> Names are metadata, not identity.

---

## 🧷 `--rm` — Automatic Cleanup

```bash
docker run --rm IMAGE
```

### What it does

* Deletes container after process exits
* Prevents clutter

### When to use it

* Tests
* One-off commands
* Experiments

📌

> `--rm` trades debuggability for cleanliness.

---

# 🧪 Hands-On Experiments (Now That Flags Are Defined)

---

## Experiment 1 — The Simplest Container

```bash
docker run hello-world
```

### What happens

* Image pulled (if missing)
* Container created
* Default command runs
* Process exits
* Container stops

Verify:

```bash
docker ps
docker ps -a
```

📌

> Stopped containers still exist.

---

## Experiment 2 — Process Lifetime = Container Lifetime

```bash
docker run busybox sleep 30
```

While running:

```bash
docker ps
```

After 30 seconds:

```bash
docker ps -a
```

📌

> Containers die when PID 1 exits.

---

## Experiment 3 — Detached Mode (`-d`)

```bash
docker run -d busybox sleep 300
```

Observe:

```bash
docker ps
```

Now try a short process:

```bash
docker run -d busybox echo "hi"
```

Check:

```bash
docker ps -a
```

📌

> Detached does NOT mean persistent.

---

## Experiment 4 — Interactive Mode (`-it`)

```bash
docker run -it busybox sh
```

Inside container:

```sh
ps
```

You will see:

* `sh` is PID 1

Exit:

```sh
exit
```

Container stops.

📌

> PID 1 controls container lifetime.

---

## Experiment 5 — Naming Containers

```bash
docker run --name demo busybox echo "named"
```

Try again:

```bash
docker run --name demo busybox echo "fail"
```

You’ll get an error.

Fix:

```bash
docker rm demo
```

📌

> Names are reserved until container is removed.

---

## Experiment 6 — Automatic Removal (`--rm`)

```bash
docker run --rm busybox echo "temporary"
```

Verify:

```bash
docker ps -a
```

Container is gone.

📌

> `--rm` deletes evidence.

---

## Experiment 7 — Overriding Image Defaults

```bash
docker run nginx
```

Stop it:

```bash
docker stop <container-id>
```

Override command:

```bash
docker run nginx ls /
```

📌

> IMAGE provides defaults; `docker run` can override them.

---

## Experiment 8 — Exit Codes Propagate

```bash
docker run busybox sh -c "exit 42"
```

Check:

```bash
docker ps -a
```

You will see:

```
Exited (42)
```

📌

> Containers behave like normal processes.

---

## 🧠 What You Have Proven (Not Memorized)

* Containers are processes
* Process lifetime = container lifetime
* Flags modify **execution context**, not logic
* STDOUT/STDERR wiring matters
* Docker is deterministic

---

## 🔑 Core Mental Model (Lock This)

> Docker does not run containers.
> Docker runs **isolated processes**.

If this is clear, Docker stops being mysterious.

---

## ➡️ Next Chapter

👉 **Chapter 4 — Understanding Containers as Processes**

We go deeper into:

* PID 1 responsibilities
* signals (`SIGTERM`, `SIGKILL`)
* why containers don’t shut down gracefully by default
* how Docker stops containers internally

