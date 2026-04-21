# PES-VCS Implementation

## Basic Details
Name: Gayathri Priya V  
SRN: PES1UG24AM105  
Course: Operating Systems  

---

## Phase 1: Object Storage

### Screenshot 1A – Test Output

Commands:

make test_objects
./test_objects


<img width="1115" height="277" alt="image" src="https://github.com/user-attachments/assets/3963efaf-57ca-481a-8ff8-5f14b83d8cb9" />

---

### Screenshot 1B – Object Storage Structure

Commands:

find .pes/objects -type f


<img width="987" height="156" alt="image" src="https://github.com/user-attachments/assets/c0e99a57-3bc3-4c9a-b532-d5a7c278861f" />

---

## Phase 2: Tree Objects

### Screenshot 2A – Tree Test Output

Commands:

make test_tree
./test_tree


<img width="1253" height="181" alt="image" src="https://github.com/user-attachments/assets/11bf652b-9768-4c12-b5d4-4432f774b28a" />

---

### Screenshot 2B – Raw Tree Object

<img width="1228" height="146" alt="image" src="https://github.com/user-attachments/assets/1b9c3366-6293-4add-b746-440180fc64b1" />

---

## Phase 3: Index (Staging Area)

### Screenshot 3A – Add and Status

Commands:

./pes init
echo "hello" > file1.txt
echo "world" > file2.txt
./pes add file1.txt file2.txt
./pes status


<img width="1317" height="692" alt="image" src="https://github.com/user-attachments/assets/788bc0ed-44f5-49d6-a7b1-1f2d13c0bfb8" />

---

### Screenshot 3B – Index File

Commands:

cat .pes/index


<img width="1292" height="116" alt="Screenshot 2026-04-21 210729" src="https://github.com/user-attachments/assets/70867525-7087-4e52-87f2-13eb9a525e1a" />

---

## Phase 4: Commit and History

### Screenshot 4A – Commit Log

Commands:

./pes log


<img width="1217" height="298" alt="image" src="https://github.com/user-attachments/assets/4c91b30a-e45f-49e8-ba21-5afb499e6f19" />

---

### Screenshot 4B – Object Store Growth

Commands:

find .pes -type f | sort


<img width="1029" height="238" alt="image" src="https://github.com/user-attachments/assets/d66f9856-76dc-48d5-bbe6-6f753929a353" />

---

### Screenshot 4C – HEAD and References

Commands:

cat .pes/refs/heads/main
cat .pes/HEAD


<img width="1290" height="206" alt="image" src="https://github.com/user-attachments/assets/948c826b-f8de-48f9-b8bc-dbcce63b0f74" />

---

## Final Integration Test

Commands:

make test-integration


<img width="1137" height="654" alt="image" src="https://github.com/user-attachments/assets/cf9fd6af-05b4-4432-a41d-0f38f6f497c5" />

<img width="1115" height="659" alt="image" src="https://github.com/user-attachments/assets/a7cc4d81-ae7d-477c-a244-be8317e7662a" />

---

## Analysis Questions

### Q5.1 Branch Checkout

A branch is stored as a file containing a commit hash. To implement checkout:

- Update `.pes/HEAD` to point to the selected branch  
- Update `.pes/refs/heads/<branch>` if needed  
- Reconstruct working directory using tree objects  

The operation is complex due to:
- File overwriting  
- Directory changes  
- Handling uncommitted changes  

---

### Q5.2 Dirty Working Directory

To detect conflicts:

- Compare working directory file metadata (mtime, size) with index  
- If file differs from index and also differs in target branch → conflict  
- Abort checkout if conflict exists  

---

### Q5.3 Detached HEAD

- HEAD points directly to a commit instead of a branch  
- New commits are not referenced by any branch  
- These commits can be recovered by creating a new branch pointing to them  

---

### Q6.1 Garbage Collection

Algorithm:

- Start from HEAD  
- Traverse all reachable commits, trees, and blobs  
- Mark visited objects using a hash set  
- Delete all unreferenced objects  

For large repositories:

- Traverse all commits (~100,000)  
- Visit associated trees and blobs  

---

### Q6.2 Race Condition in GC

Problem:

- Garbage collector deletes objects while commit is being created  

Race condition:

- Commit references object not fully written  
- GC deletes it → repository corruption  

Solution:

- Use locking mechanisms  
- Mark objects as in-use  
- Git avoids deleting recently created objects  
