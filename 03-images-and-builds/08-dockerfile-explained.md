
# 📘 Chapter 8 — Dockerfile Explained Line by Line (WHY Each Instruction Exists)

> 🧠 A Dockerfile is **not a script**.  
> It is a **recipe for building an immutable filesystem**.

This chapter teaches you how to **write Dockerfiles deliberately**, not by copying.

---

## ❓ Why This Chapter Exists

Most Dockerfiles on the internet:
- are copy-pasted
- are bloated
- are slow to build
- hide problems instead of solving them

People know *what* to type, but not:
- why instructions exist
- how layers are created
- why images become huge
- why containers behave unexpectedly

This chapter fixes that.

---

## 🧠 Core Mental Model (Lock This In First)

> **Each Dockerfile instruction creates a new image layer.**

- Layers are **read-only**
- Layers are **cached**
- Order matters
- Mistakes are permanent once baked in

If you remember only one thing from this chapter, remember this.

---

## 📐 What a Dockerfile Really Is

A Dockerfile is:
- a **declarative build file**
- read top → bottom
- executed by the Docker **builder**
- used to produce an **image**, not a container

📌  
> Dockerfiles build images. Containers come later.

---

# 🔧 Dockerfile Instructions We Will Cover

We will cover **only core, real-world instructions**:

- `FROM`
- `RUN`
- `COPY` vs `ADD`
- `WORKDIR`
- `ENV`
- `CMD`
- `ENTRYPOINT`

Each instruction is defined **before examples**.

---

## 🧷 `FROM` — Base Image (Required)

### What it is
Defines the **starting filesystem** for your image.

```Dockerfile
FROM ubuntu:22.04
````

### Why it exists

You never start from nothing.
You always build **on top of another image**.

### What it does NOT do

* ❌ Does NOT install software
* ❌ Does NOT run containers

📌

> `FROM` answers: *“What am I building on?”*

---

### 🧪 Experiment

```bash
docker pull ubuntu:22.04
docker inspect ubuntu:22.04
```

Observe:

* filesystem
* metadata
* no running process

---

## 🧷 `RUN` — Execute Commands at Build Time

### What it is

Runs a command **during image build**, not at runtime.

```Dockerfile
RUN apt update && apt install -y nginx
```

### Why it exists

* To install packages
* To prepare filesystem
* To bake dependencies into the image

### What it does NOT do

* ❌ Does NOT run when container starts
* ❌ Does NOT stay “active”

📌

> `RUN` modifies the image, not the container.

---

### 🧪 Experiment

```Dockerfile
FROM busybox
RUN echo "built at image time" > /build.txt
```

Build and run:

```bash
docker build -t run-test .
docker run run-test cat /build.txt
```

📌

> Output proves `RUN` happened at build time.

---

## 🧷 `COPY` vs `ADD` — Bringing Files In

### `COPY` (Preferred)

```Dockerfile
COPY app.py /app/app.py
```

* Copies local files
* Simple
* Predictable

---

### `ADD` (Rarely Needed)

```Dockerfile
ADD archive.tar.gz /app/
```

Extra features:

* auto-extract archives
* fetch remote URLs

📌 **Why `COPY` is preferred**

* Less magic
* More explicit
* Fewer surprises

📌

> Use `ADD` only when you **need its extra behavior**.

---

## 🧷 `WORKDIR` — Set Working Directory

### What it is

Sets the default directory for:

* `RUN`
* `CMD`
* `ENTRYPOINT`

```Dockerfile
WORKDIR /app
```

### Why it exists

* Avoids repeated `cd`
* Improves readability
* Reduces mistakes

📌

> `WORKDIR` changes context, not filesystem.

---

### 🧪 Experiment

```Dockerfile
FROM busybox
WORKDIR /data
RUN pwd > location.txt
```

Run container and verify.

---

## 🧷 `ENV` — Environment Variables

### What it is

Defines environment variables **inside the image**.

```Dockerfile
ENV APP_ENV=production
```

### Why it exists

* Default configuration
* Runtime customization
* Image reuse

### What it does NOT do

* ❌ Does NOT enforce configuration
* ❌ Can be overridden at runtime

📌

> `ENV` provides defaults, not guarantees.

---

## 🧷 `CMD` — Default Runtime Command

### What it is

Defines **what runs when the container starts**.

```Dockerfile
CMD ["nginx", "-g", "daemon off;"]
```

### Why it exists

* Defines container purpose
* Provides sane default behavior

### Key behavior

* Can be overridden by `docker run`

📌

> `CMD` is a suggestion, not a rule.

---

## 🧷 `ENTRYPOINT` — Fixed Execution Path

### What it is

Defines the **main executable**.

```Dockerfile
ENTRYPOINT ["python", "app.py"]
```

### Why it exists

* Enforces how container is run
* Useful for CLI-style containers

### CMD + ENTRYPOINT together

* `ENTRYPOINT` → executable
* `CMD` → default arguments

📌

> ENTRYPOINT defines *what*. CMD defines *how*.

---

## 🧠 CMD vs ENTRYPOINT (Critical Difference)

| Aspect            | CMD | ENTRYPOINT |
| ----------------- | --- | ---------- |
| Override easily   | ✅   | ❌          |
| Defines default   | ✅   | ❌          |
| Enforces behavior | ❌   | ✅          |

Use wisely.

---

## 🧪 Full Minimal Example (Everything Together)

```Dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY app.py .
ENV APP_ENV=prod
CMD ["python", "app.py"]
```

Build and run:

```bash
docker build -t my-app .
docker run my-app
```

---

## 🚫 Common Beginner Mistakes (Very Important)

❌ Using `RUN` instead of `CMD`
❌ Putting secrets in `ENV`
❌ Not using `WORKDIR`
❌ Large images from too many `RUN` layers
❌ Treating Dockerfile like a bash script

Each mistake leads to:

* slow builds
* insecure images
* painful debugging

---

## 🧠 What You Now Understand

* Dockerfiles create **immutable layers**
* Instructions are build-time vs run-time
* Order affects cache
* Images are recipes, not environments

You are now ready to **build images intentionally**.

---

## 🔑 Core Mental Model (Final Lock)

> **Dockerfiles describe how to build a filesystem — not how to run a server.**

If you get this wrong, everything feels weird.

---

## ➡️ Next Chapter

👉 **Chapter 9 — Building Images (`docker build`) — What Actually Happens**

Next, we go deep into:

* build context
* `.dockerignore`
* layer caching
* why builds are slow or fast
* why images explode in size


