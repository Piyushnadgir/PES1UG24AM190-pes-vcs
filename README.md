# PES-VCS — Version Control System Implementation

### Name: Piyush G Nadgir

### SRN: PES1UG24AM190

---

## Project Overview

This project is a custom Version Control System (VCS) built from scratch in C, modeled after the internal architecture of Git. It implements content-addressable storage, directory tree snapshots, a staging area (index), and a persistent commit history.

---

## Architecture & OS Concepts

* **Content-Addressable Storage:** Files are stored based on their SHA-256 hash rather than filenames.
* **Atomic Writes:** Implementation of the temp-file-then-rename pattern to ensure filesystem integrity.
* **Directory Sharding:** Sharding the `.pes/objects` directory to optimize lookups.
* **Metadata Tracking:** Utilizing `stat` to track `mtime` and `size` for fast change detection.

---

## Phases of Development & Evidence

### Phase 1: Object Storage

**Screenshot 1A (Tests):**
![1A](./screenshots/1A_test_objects.png)
Output showing all tests passing for blob storage and deduplication.

**Screenshot 1B (Sharding):**
![1B](./screenshots/1B_find_objects.png)
Directory structure showing sharded objects under `.pes/objects/XX/`.

---

### Phase 2: Tree Objects

**Screenshot 2A (Tests):**
![2A](./screenshots/2A_test_tree.png)
Successful serialization and parsing of tree hierarchies.

**Screenshot 2B (Binary View):**
![2B](./screenshots/2B_xxd_tree.png)
Hex dump using `xxd` showing the binary structure of a Tree object.

---

### Phase 3: The Index (Staging Area)

**Screenshot 3A (Status):**
![3A](./screenshots/3A_pes_status.png)
Command output of `./pes status` showing staged files.

**Screenshot 3B (Index File):**
![3B](./screenshots/3B_cat_index.png)
Raw content of `.pes/index` showing metadata and hashes.

---

### Phase 4: Commits and History

**Screenshot 4A (Commit Log - View 1):**
![4A-1](./screenshots/4A_pes_log.png)

**Screenshot 4A (Commit Log - View 2):**
![4A-2](./screenshots/4A_pes_log_2_.PNG)

Verification of commit creation and linked history.

**Screenshot 4B (Object Growth):**
![4B](./screenshots/4B_find_pes.png)
Object store growth after multiple commits.

**Screenshot 4C (References):**
![4C](./screenshots/4C_head_refs.png)
Current HEAD pointing to the latest commit hash.

**Screenshot 4 Test (Integration):**
![4test](./screenshots/final_integration.png)

**Additional Integration Views:**
![extra1](./screenshots/final_integration_2_.PNG)
![extra2](./screenshots/final_integration_3_.PNG)

---

## Analysis Questions

### Section 5: Branching and Checkout

**Q5.1 Implementation of `checkout`:**
To implement `pes checkout <branch>`, the `HEAD` file would be updated to point to the new branch reference. The working directory must then be updated by reading the Tree object associated with the target commit. This requires performing a safe diff: deleting files not present in the target tree, adding missing ones, and updating modified ones without losing uncommitted changes.

**Q5.2 Dirty Directory Detection:**
The working directory can be compared using `stat` (mtime and size) against the index entries. If differences exist, the directory is considered "dirty". Additionally, comparing index hashes with the target commit’s Tree object helps detect divergence.

**Q5.3 Detached HEAD:**
When committing in a detached HEAD state, the new commit is created but no branch reference is updated. These commits remain in the object store and can be recovered by manually creating a branch pointing to the commit hash.

---

### Section 6: Garbage Collection (GC)

**Q6.1 Reachability Algorithm:**
A Mark-and-Sweep algorithm can be used:

* Mark all reachable objects starting from `HEAD` and `refs/heads/`
* Sweep and delete all unmarked objects from `.pes/objects/`

**Q6.2 Race Conditions in GC:**
If GC runs concurrently with a commit, it may delete objects that are not yet referenced. This can be avoided using a grace period or by marking recently created objects as protected.

---

## How to Run

```bash
make pes
./pes init
./pes add <filename>
./pes commit -m "Your message"
```
