
# 📘 Chapter 7 — What Is an Image Really?

> 🧠 If containers are **processes**,  
> then images are **frozen filesystems**.

This chapter explains images without mysticism — using experiments.

---

## ❓ Why This Chapter Exists

Most people think:

> “An image is just something you run.”

That leads to confusion about:
- why images are read-only
- why containers don’t persist changes
- why rebuilding is normal
- why images can be shared safely

This chapter fixes your **image mental model**.

---

## 🧠 Core Idea (Lock This In)

> **A Docker image is a read-only filesystem snapshot + metadata.**

It is:
- NOT a running thing
- NOT an environment
- NOT mutable

Images do **nothing** until a container is created from them.

---

## 🧱 Images vs Containers (Clear Separation)

| Image | Container |
|----|----|
| Read-only | Writable |
| Static | Running or stopped |
| Blueprint | Instance |
| Reusable | Disposable |

📌  
> An image is to a container what a class is to an object.

---

## 🧪 Experiment 1 — Images Exist Without Containers

List images:

```bash
docker images
````

Now remove all containers (safe):

```bash
docker rm -f $(docker ps -aq)
```

List images again:

```bash
docker images
```

📌 **Proof**

> Images exist independently of containers.

---

## 🧠 What’s Inside an Image?

An image contains:

* filesystem layers
* metadata (CMD, ENV, ENTRYPOINT)
* no running processes
* no state

Think of it as:

> a **tarball of a Linux filesystem**, stacked in layers.

---

## 🧪 Experiment 2 — Images Are Read-Only

Run a container:

```bash
docker run -it busybox sh
```

Inside container:

```sh
touch /myfile
ls /
exit
```

Now start **another container** from same image:

```bash
docker run busybox ls /
```

📌

> `myfile` is **gone**.

Why?

* image didn’t change
* container filesystem was temporary

---

## 🧠 Why Images Are Read-Only (Critical Design Choice)

If images were writable:

* builds would be non-reproducible
* sharing would be unsafe
* debugging would be impossible
* rollbacks would be unreliable

Immutability gives:

* consistency
* reproducibility
* safety
* trust

📌

> Images are immutable so systems can be predictable.

---

## 🧱 Image Layers (Important)

Images are built in **layers**.

Each layer:

* represents a filesystem change
* is read-only
* can be reused across images

---

## 🧪 Experiment 3 — Seeing Image Layers

Inspect image history:

```bash
docker history nginx
```

You will see:

* multiple layers
* each with size and command
* base layers reused

📌

> Layers explain why images can be large *and* efficient.

---

## 🧠 How Containers Use Images (Union Filesystem)

When a container starts:

* image layers are stacked (read-only)
* a **thin writable layer** is added on top
* all writes go to that top layer

This is called:

* union filesystem
* overlay filesystem

📌

> Containers write on top of images — never into them.

---

## 🧪 Experiment 4 — Writable Layer Proof

Run:

```bash
docker run -it busybox sh
```

Inside:

```sh
echo hello > /data.txt
exit
```

Restart same container:

```bash
docker start -ai <container-id>
cat /data.txt
```

Now remove container:

```bash
docker rm <container-id>
```

Run new container from same image:

```bash
docker run busybox cat /data.txt
```

📌

> Writable layer dies with the container.

---

## 🧠 Image vs Container (Final Lock)

* Images → immutable, reusable, sharable
* Containers → temporary, stateful, disposable

If you want persistence:

* ❌ not inside container filesystem
* ✅ use volumes (Chapter 13)

---

## 🚫 Common Image Misunderstandings

❌ “Images are like installed systems”
❌ “Changes inside container update image”
❌ “Rebuilding is wasteful”
❌ “Images are heavy VMs”

All false — and now you know why.

---

## 🧠 What You Have Proven

* Images exist without containers
* Images are read-only
* Containers add a writable layer
* Deleting containers deletes changes
* Layers enable reuse and efficiency

No theory — all verified.

---

## 🔑 Core Mental Model (Lock This)

> **Images are frozen filesystem snapshots.
> Containers are writable process instances on top.**

Once this clicks, Dockerfile behavior becomes obvious.

---

## ➡️ Next Chapter

👉 **Chapter 8 — Dockerfile Explained Line by Line (WHY Each Instruction Exists)**

Now we answer:

* how images are built
* why each instruction exists
* why bad Dockerfiles cause pain
* how layers are created intentionally


