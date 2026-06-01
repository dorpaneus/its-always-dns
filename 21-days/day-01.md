<div align="center">

[⬅️ Prev](../README.md) &nbsp;·&nbsp; [🏠 Home](../README.md) &nbsp;·&nbsp; [Next: Day 2 ➡️](./day-02-processes-namespaces.md)

</div>

---

# 🐧 Day 1 - Linux Fundamentals & Storage Models

> [!NOTE]  
> **The Goal:** Google interviewers (especially for SRE and Systems Engineering) probe past the commands to see if you understand the *kernel abstractions*. 
> * "Disk is full but `df` says space is available" → **Inode exhaustion**. 
> * "I deleted the file but disk is still full" → **An open File Descriptor (FD) holding the inode**. 
> * "Why is my database slow on this distributed filesystem?" → **POSIX compliance overhead vs. Object Storage**.
> 
> Today builds the foundational mental model that makes these answers automatic.

---

## 🧠 Morning Block — The Unix File Model & VFS

### 1a. Everything is a file (and the VFS Layer)

To understand Linux file systems at a senior level, you must understand the **Virtual File System (VFS)** layer. The Linux kernel uses VFS to abstract the implementation details of different file systems (like `ext4`, `xfs`, `tmpfs`, or `NFS`). 

When an application calls `read()` or `write()`, it talks to the VFS. The VFS then translates that into the specific driver.

**The Three Pillars of a "File":**
1. **The Inode (Index Node):** The true representation of the file on disk. It holds metadata (permissions, owner, size, timestamps, link count) and pointers to the physical data blocks. *It does not contain the filename.*
2. **The Directory Entry (dentry):** The human-readable mapping. A directory is just a file whose data blocks contain a table pairing a `filename` with an `inode_number`.
3. **The File Descriptor (FD):** The in-memory reference used by an active process. When a process opens a file, the kernel creates an FD pointing to an open file description, which points to the underlying inode. 

### 1b. Linux File Types Reference

When you run `ls -l`, the very first character indicates the file type. 

| Character | File Type | SRE Context / Use Case |
| :---: | :--- | :--- |
| `-` | **Regular File** | Standard data (logs, binaries). |
| `d` | **Directory** | A table mapping names to inodes. |
| `l` | **Symbolic Link** | A soft link containing a text path to another file. |
| `b` | **Block Device** | Unbuffered access to hardware (e.g., `/dev/sda`). Reads/writes in chunks. |
| `c` | **Character Device** | Unbuffered, sequential access (e.g., `/dev/null`, `/dev/urandom`, terminals). |
| `p` | **Named Pipe (FIFO)** | IPC. Data written to one end is read from the other (e.g., `mkfifo`). |
| `s` | **Socket** | Network or local IPC (e.g., Docker daemon socket `/var/run/docker.sock`). |

> [!IMPORTANT]
> **☁️ The GCP Bridge: Persistent Disks vs. GCS**
> Interviewers love testing if you know where POSIX ends and Cloud starts.
> * **Persistent Disks (PD):** These are block devices (`b`). When you format a PD with `ext4` and mount it to a GCE instance, all rules of inodes, hard links, and VFS apply. If it runs out of inodes, writes fail even if the GCP console shows 50GB free.
> * **Google Cloud Storage (GCS):** This is object storage, *not* a POSIX file system. There are no inodes, no directories, and no FDs. "Directories" in GCS are just string prefixes in the object key (`folder/file.txt`). 
> * **The FUSE Trap:** If you mount GCS to a VM using Cloud Storage FUSE, the Linux kernel *pretends* it's POSIX. However, operations like renaming a directory (`mv`) aren't simple metadata updates; FUSE has to issue API calls to copy and delete *every single object* under that prefix. This is a classic interview discussion point regarding performance degradation.

### 1c. Permissions, ACLs, and the `+` Sign

Octal cheat: `r=4, w=2, x=1`. 
* `755` = `rwxr-xr-x`
* `644` = `rw-r--r--`

**Special bits (High-frequency interview territory):**
* **setuid (4xxx):** Process runs as the file's *owner*. Shows as `s` in owner-execute (`-rwsr-xr-x`). Why non-root users can run `passwd` (it needs root to write `/etc/shadow`).
* **setgid (2xxx):** Runs as the file's *group*. On a directory (`drwxrwsr-x`), new files inherit the directory's group instead of the creator's group. Essential for shared workspaces.
* **sticky (1xxx):** On a directory (`drwxrwxrwt`), only the file's owner (or root) can delete files within it. Essential for `/tmp`.

**POSIX ACLs (Access Control Lists):**
Standard permissions are limited to one owner and one group. If you see a `+` at the end of the permission string (`-rw-r--r--+`), it means Extended ACLs are applied. You must use `getfacl <file>` to see them, allowing you to grant specific access to a secondary user without changing the file's primary group.

### 1d. Hard vs. Symbolic Links

* **Hard Link (`ln`):** A new directory entry pointing to the *exact same inode*. Link count increases. Data survives as long as link count > 0. **Constraint:** Cannot cross filesystems/partitions (inode numbers are only unique locally). Cannot hard-link directories (prevents infinite loops).
* **Symlink (`ln -s`):** A new file with its own inode. Its data block contains the literal string path of the target. If the target is deleted, it becomes a "dangling" link.

---

## 💻 Midday Block — Hands-on Labs

*Predict the outcome before pressing <kbd>Enter</kbd>.*

### Lab 1: Inodes & Link Constraints
```bash
mkdir ~/day1 && cd ~/day1
touch source.txt
ln source.txt hard.txt
ln -s source.txt soft.txt
ls -li
```
* *Predict:* `source.txt` and `hard.txt` share the same inode and have a link count of 2. `soft.txt` has a different inode.

### Lab 2: Extended ACLs (The hidden permissions)
```bash
touch restricted.txt
chmod 600 restricted.txt
ls -l restricted.txt      # Only owner has access

# Grant a specific dummy user (e.g., 'nobody') read access via ACL
sudo setfacl -m u:nobody:r restricted.txt
ls -l restricted.txt      # Notice the '+' sign added to the permissions!
getfacl restricted.txt    # View the true permissions
```

### Lab 3: The "Deleted but Disk Full" Recovery Trick
This is a classic senior SRE scenario. Open two terminals.

```bash
# Terminal 1
echo "critical_data" > massive_log.txt
tail -f massive_log.txt   # Holds an open File Descriptor (FD)
```
```bash
# Terminal 2
rm massive_log.txt
ls massive_log.txt        # It's gone from the directory
df -h .                   # Space is NOT freed!
```
*Why?* The `rm` command removed the directory entry and dropped the link count to 0. However, the VFS knows Terminal 1 (`tail`) still has an open FD. The data blocks are kept alive.

**The Senior SRE Move (Recovering the deleted file):**
```bash
# In Terminal 2, find the process holding the deleted file
lsof +L1 

# Look at the PID and the FD number (e.g., PID 1234, FD 3)
# You can actually read the deleted file directly out of memory/procfs!
cat /proc/<PID_HERE>/fd/<FD_NUMBER_HERE>

# To free the space, kill the process
kill <PID_HERE>
```

### Lab 4: Timestamps & `stat`
```bash
touch time.txt
stat time.txt

cat time.txt > /dev/null  # Updates atime (Access)
echo "data" >> time.txt   # Updates mtime (Modify Data) & ctime (Change Inode)
chmod 777 time.txt        # Updates ctime ONLY (Metadata changed, data did not)
stat time.txt
```

---

## 🎯 Afternoon Block — Drills + Behavioral

### Self-Check Drills (Google GCA / RRK Style)

Answer out loud. Force structure.

1. Walk through every column of `ls -l /etc/shadow`.
2. A junior engineer complains their app is crashing with "No space left on device", but `df -h` shows 50% free space. What is the cause, how do you verify, and how do you fix it?
3. What is the architectural difference between modifying a file's permission on a local `ext4` disk versus updating permissions on a Google Cloud Storage object?
4. You accidentally deleted a critical configuration file, but you know the `nginx` process still has it loaded in memory. How do you get the contents back?
5. Why does an empty directory have a link count of 2?

<details>
<summary><strong>Answers</strong> (click to reveal)</summary>

1. Type+perms / hard-link count / owner / group / size (in bytes) / mtime / name. 
2. **Inode exhaustion.** Verify with `df -i`. Cause: millions of tiny files (like session files or un-rotated logs). Fix: Find the directory with the most files (`find / -xdev -type f | cut -d "/" -f 2 | sort | uniq -c | sort -n`) and delete them.
3. `ext4` permissions are stored locally in the file's inode and evaluated by the kernel's VFS layer instantaneously. GCS permissions are evaluated via Cloud IAM; updating them requires an API call to Google's control plane, which propagates eventually consistent IAM policies.
4. Find the PID of `nginx`. Look in `/proc/<PID>/fd/` for the symlink pointing to the `(deleted)` file. Copy the contents of that FD to a new file: `cat /proc/<PID>/fd/<FD_NUM> > /etc/nginx/recovered.conf`.
5. The directory entry itself counts as 1. The `.` (current directory) hidden entry inside the directory counts as the 2nd link to the same inode.
</details>

### 🤝 Behavioral — Story #1: Role-Related Knowledge (RRK) & Troubleshooting

Today's Dimension: **RRK / General Cognitive Ability (GCA)**

Google wants to see how you systematically break down a complex, ambiguous problem. 

Prompt to write a STAR story for:
> "Tell me about a time you had to troubleshoot a complex, systemic issue that others could not figure out. How did you isolate the root cause?"

Write it down (don't memorize — internalize beats):
* **Situation** — What was the system, the scale, and the impact of the outage? (Keep it brief).
* **Task** — What was your responsibility?
* **Action** — *This is the most important part.* Detail your scientific method. "First I checked X, which ruled out Y. Then I used [Tool] to look at [Metric], which led me to the OS layer..." Show your First Principles thinking.
* **Result** — How did you mitigate it, and what automation/observability did you add to prevent it?

Say it out loud. Target ~3 minutes. Ensure you emphasize *how* you thought, not just what button you pressed.
