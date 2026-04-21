# PES-VCS — Version Control System Implementation

### Name: Piyush G Nadgir

### SRN: PES1UG24AM190

---

## Project Overview

This project is a custom Version Control System (VCS) built from scratch in C, modeled after the internal architecture of Git. It implements content-addressable storage, directory tree snapshots, a staging area (index), and a persistent commit history.

---

## Architecture & OS Concepts

* Content-addressable storage using SHA-256 hashes
* Atomic writes using temp-file-then-rename pattern
* Directory sharding in `.pes/objects/`
* Metadata tracking using `stat` (mtime and size)

---

## Phases of Development & Evidence

### Phase 1: Object Storage

**Screenshot 1A (Tests)**
![1A](screenshots/1A_test_objects.png)

**Screenshot 1B (Sharding)**
![1B](screenshots/1B_find_objects.png)

---

### Phase 2: Tree Objects

**Screenshot 2A (Tests)**
![2A](screenshots/2A_test_tree.png)

**Screenshot 2B (Binary View)**
![2B](screenshots/2B_xxd_tree.png)

---

### Phase 3: The Index (Staging Area)

**Screenshot 3A (Status)**
![3A](screenshots/3A_pes_status.png)

**Screenshot 3B (Index File)**
![3B](screenshots/3B_cat_index.png)

---

### Phase 4: Commits and History

**Screenshot 4A (Commit Log - View 1)**
![4A-1](screenshots/4A_pes_log.png)

**Screenshot 4A (Commit Log - View 2)**
![4A-2](screenshots/4A_pes_log_2_.png)

**Screenshot 4B (Object Growth)**
![4B](screenshots/4B_find_pes.png)

**Screenshot 4C (References)**
![4C](screenshots/4C_head_refs.png)

**Screenshot 4 Test (Integration)**
![4test](screenshots/final_integration.png)

**Additional Integration Views**
![extra1](screenshots/final_integration_2_.png)
![extra2](screenshots/final_integration_3_.png)

---

## Analysis Questions

### Section 5: Branching and Checkout

**Q5.1 Implementation of checkout**
Update the HEAD file to point to the new branch reference. Then update the working directory by reading the Tree object of the target commit. This involves safely removing, adding, and modifying files without losing uncommitted changes.

**Q5.2 Dirty Directory Detection**
Compare current file metadata (`mtime`, `size`) with index entries. If mismatched, mark as dirty. Also compare index hashes with the target commit’s Tree object.

**Q5.3 Detached HEAD**
Commits created in detached HEAD are not referenced by any branch. They remain in the object store and can be recovered by manually creating a branch pointing to the commit hash.

---

### Section 6: Garbage Collection

**Q6.1 Reachability Algorithm**
Use Mark-and-Sweep:

* Mark all objects reachable from HEAD and refs
* Delete all unmarked objects

**Q6.2 Race Conditions in GC**
GC may delete objects created during an ongoing commit. This can be avoided using a grace period or protecting recently created objects.

---

## How to Run

```bash
make pes
./pes init
./pes add <filename>
./pes commit -m "Your message"
```
