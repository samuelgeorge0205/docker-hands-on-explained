
# 📘 Chapter 6 — Inspecting Reality: `ps`, `logs`, `inspect`, `stats`

> 🧠 Docker debugging is not intuition.  
> It is **observation**.

This chapter teaches you how to **see what Docker is actually doing**.

---

## ❓ Why This Chapter Exists

Most Docker failures sound like this:

- “The container is running but nothing works”
- “It exited for no reason”
- “Docker is buggy”
- “It worked yesterday”

In reality:
- Docker is deterministic
- The kernel is honest
- The truth is visible — if you know where to look

This chapter gives you the **four tools** that remove guessing.

---

## 🧠 The Core Debugging Rule (Lock This In)

> **Never debug Docker by assumption. Always inspect state.**

Docker gives you:
- process state
- logs
- configuration
- resource usage

You must learn to **read them in the right order**.

---

# 🔧 The Four Inspection Commands

We will cover:

1. `docker ps` → *What exists & what is running*
2. `docker logs` → *What the process said*
3. `docker inspect` → *What Docker knows*
4. `docker stats` → *What resources are being used*

Each answers a **different question**.

---

## 🧷 `docker ps` — What Is Running?

### What it is
Lists containers known to Docker.

```bash
docker ps
````

### What it shows

* running containers only
* container ID
* image
* command
* uptime
* port mappings
* names

📌

> `docker ps` answers: *“What is alive right now?”*

---

### Show all containers (important)

```bash
docker ps -a
```

This includes:

* exited containers
* failed containers
* stopped containers

📌

> Most “missing containers” are just stopped.

---

### 🧪 Hands-On Experiment

```bash
docker run busybox echo hello
docker ps
docker ps -a
```

Observe:

* not running
* but still exists

---

## 🧷 `docker logs` — What Did the Process Say?

### What it is

Shows **STDOUT and STDERR** of the container process.

```bash
docker logs <container>
```

### Why this matters

Containers do NOT log to files by default.
They log to:

* STDOUT
* STDERR

Docker captures those streams.

📌

> If the app didn’t print it, Docker can’t show it.

---

### 🧪 Hands-On Experiment

```bash
docker run --name log-test busybox sh -c "echo hello; echo error >&2"
docker logs log-test
```

You will see:

* both outputs

---

### Follow logs (live)

```bash
docker logs -f <container>
```

Equivalent to:

```bash
tail -f
```

Stop with `Ctrl+C`.

---

### ❌ Common Mistake

❌ Expecting log files inside container
❌ SSH-ing into container to check logs

📌

> Containers log outward, not inward.

---

## 🧷 `docker inspect` — The Source of Truth

### What it is

Returns **full JSON metadata** about a container, image, volume, or network.

```bash
docker inspect <container>
```

This is what Docker **actually knows**, not what you assume.

---

### What you should look for (key fields)

You do NOT read all of it. You scan for:

* `"State"`

  * `Status`
  * `Running`
  * `ExitCode`
  * `Error`
* `"Config"`

  * `Cmd`
  * `Env`
* `"HostConfig"`

  * `RestartPolicy`
  * `PortBindings`
* `"Mounts"`

📌

> `inspect` explains *why* something behaved the way it did.

---

### 🧪 Hands-On Experiment

```bash
docker run --name inspect-test -d -p 8080:80 nginx
docker inspect inspect-test
```

Now search inside output:

* `PortBindings`
* `RestartPolicy`
* `Cmd`

This is **ground truth**.

---

### Extract specific fields (very useful)

```bash
docker inspect -f '{{.State.ExitCode}}' inspect-test
```

📌

> Filters turn JSON into answers.

---

## 🧷 `docker stats` — What Resources Are Being Used?

### What it is

Live view of:

* CPU usage
* memory usage
* network I/O
* block I/O

```bash
docker stats
```

This data comes from **cgroups**.

📌

> `stats` answers: *“Is this container hurting my system?”*

---

### 🧪 Hands-On Experiment

Run a CPU-heavy container:

```bash
docker run -d busybox sh -c "while true; do :; done"
```

Now:

```bash
docker stats
```

Observe:

* CPU usage spikes

Stop container:

```bash
docker stop <container>
```

---

## 🧠 How to Debug Systematically (Order Matters)

When something is wrong, follow this order:

1️⃣ `docker ps -a`
→ Does the container exist? Is it running?

2️⃣ `docker logs <container>`
→ What did the process output?

3️⃣ `docker inspect <container>`
→ What configuration and state does Docker report?

4️⃣ `docker stats`
→ Is it resource-starved?

📌

> Skipping steps causes confusion.

---

## 🚫 Common Debugging Anti-Patterns

❌ Restarting containers blindly
❌ Rebuilding images without checking logs
❌ Assuming Docker bugs
❌ Guessing configuration

Docker always tells you the truth — if you ask correctly.

---

## 🧠 What You Now Understand

* `docker ps` shows existence and state
* `docker logs` shows process output
* `docker inspect` shows Docker’s knowledge
* `docker stats` shows kernel-level resource usage

These four commands eliminate **90% of Docker confusion**.

---

## 🔑 Core Mental Model (Lock This)

> **Docker debugging is reading state, not guessing intent.**

If you trust inspection, Docker becomes boring — and boring is good.

---

## ➡️ Next Chapter

👉 **Chapter 7 — What Is an Image Really?**

Now that you can:

* run containers
* inspect them
* debug them

…it’s time to understand **what images actually are**
and why they behave the way they do.

