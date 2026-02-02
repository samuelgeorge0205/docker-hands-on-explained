
# 📘 Chapter 9 — Building Images (`docker build`) — What Actually Happens

> 🧠 If Dockerfiles describe *what* to build,  
> `docker build` explains *how* it actually happens.

This chapter removes all mystery from image builds.

---

## ❓ Why This Chapter Exists

Most people run:

```bash
docker build -t myimage .
````

And have **no idea**:

* what `.` really means
* what gets sent to Docker
* why builds are slow or fast
* why changing one line invalidates everything
* why images suddenly become huge

This chapter fixes that.

---

## 🧠 Core Mental Model (Lock This In First)

> **`docker build` sends a build context to the Docker daemon
> and executes Dockerfile instructions layer by layer.**

If you misunderstand *build context*, everything breaks quietly.

---

## 📐 Basic Syntax of `docker build`

```bash
docker build [OPTIONS] PATH | URL | -
```

Most common usage:

```bash
docker build -t myimage .
```

Let’s break this apart **explicitly**.

---

## 🧷 `.` — The Build Context (Critical Concept)

### What `.` means

`.` is **not** “current folder for Dockerfile only”.

It means:

> “Send the entire current directory to the Docker daemon.”

That includes:

* source code
* binaries
* `.git/`
* secrets
* node_modules
* anything in that directory

📌

> Docker cannot see files outside the build context.

---

## 🧪 Experiment 1 — Proving Build Context

Create files:

```bash
mkdir build-test
cd build-test
echo "hello" > file.txt
```

Dockerfile:

```Dockerfile
FROM busybox
COPY file.txt /file.txt
CMD ["cat", "/file.txt"]
```

Build:

```bash
docker build -t context-test .
docker run context-test
```

Now try copying a file **outside** the directory.

📌 **Proof**

> Only files inside build context are visible.

---

## 🧷 Why Large Contexts Are Dangerous

If your build context contains:

* `.git/`
* `node_modules/`
* logs
* binaries

Then:

* builds are slow
* images become bloated
* secrets may leak

This is why `.dockerignore` exists.

---

## 🧷 `.dockerignore` — Controlling the Context

### What it is

A file that tells Docker:

> “Do NOT send these files to the daemon.”

Example:

```dockerignore
.git
node_modules
*.log
.env
```

### Why it exists

* smaller build context
* faster builds
* safer images

📌

> `.dockerignore` affects build speed **before Dockerfile runs**.

---

## 🧪 Experiment 2 — Measuring Context Size

Build with verbose output:

```bash
docker build .
```

Watch for:

```
Sending build context to Docker daemon  XX.XMB
```

Add `.dockerignore`, rebuild, compare size.

📌

> Smaller context = faster build.

---

## 🧷 `-t` — Tagging the Image

```bash
docker build -t myimage:1.0 .
```

### What it does

* Assigns a **name:tag** to the image
* Makes image easy to reference

### What it does NOT do

* ❌ Does NOT change image content
* ❌ Does NOT affect build behavior

📌

> Tags are labels, not versions (yet).

---

## 🧠 What Happens During a Build (Step by Step)

For each Dockerfile instruction:

1. Docker checks cache
2. If cache hit → reuse layer
3. If cache miss → execute instruction
4. Create new layer
5. Move to next instruction

📌

> Docker builds **top → bottom**, layer by layer.

---

## 🧷 Layer Caching (Why Order Matters)

### Key rule

> **If a layer changes, all layers after it are rebuilt.**

---

### 🧪 Experiment 3 — Cache Invalidation

Dockerfile (bad order):

```Dockerfile
FROM python:3.11
COPY . /app
RUN pip install flask
```

Change a single source file → entire build reruns.

Better order:

```Dockerfile
FROM python:3.11
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . /app
```

📌

> Stable layers first. Volatile layers last.

---

## 🧷 Why Builds Are Slow

Common causes:

* huge build context
* no `.dockerignore`
* bad instruction order
* rebuilding dependencies every time
* network dependency inside `RUN`

Docker is usually not the problem.

---

## 🧷 Image Size Explosions (Root Causes)

Images grow large because of:

* installing build tools and not removing them
* too many layers with temp files
* caching package managers
* using full OS base images

📌

> Image size problems are **Dockerfile design problems**.

(Solved properly in Chapter 10.)

---

## 🧪 Experiment 4 — Inspecting Image Size

```bash
docker images
```

Inspect layers:

```bash
docker history myimage
```

Look for:

* large layers
* unexpected commands

---

## 🧠 Build-Time vs Run-Time (Final Clarity)

| Build Time     | Run Time          |
| -------------- | ----------------- |
| `docker build` | `docker run`      |
| `RUN`          | `CMD`             |
| Creates image  | Creates container |
| Happens once   | Happens every run |

Confusing these causes **90% of Docker mistakes**.

---

## 🚫 Common Build Anti-Patterns

❌ Copying entire project early
❌ No `.dockerignore`
❌ Installing deps after copying code
❌ Baking secrets into images
❌ Rebuilding instead of caching

Each leads to slow, unsafe builds.

---

## 🧠 What You Now Understand

* Build context is explicit
* Dockerfile runs inside the context
* `.dockerignore` controls what Docker sees
* Layers are cached intentionally
* Order determines speed and size

You now understand **why builds behave the way they do**.

---

## 🔑 Core Mental Model (Lock This)

> **`docker build` is deterministic, layer-based, and cache-driven.**

If you design for cache, Docker feels fast.
If you fight it, Docker feels painful.

---

## ➡️ Next Chapter

👉 **Chapter 10 — Multi-Stage Builds (WHY They Reduce Size)**

Next, we solve:

* massive images
* build tool pollution
* runtime minimalism

This is where images become **production-grade**.
