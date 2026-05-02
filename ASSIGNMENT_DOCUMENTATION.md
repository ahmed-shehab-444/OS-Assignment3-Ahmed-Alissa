# Assignment 3 - Complete Documentation

**Student Name**: [Ahmed shehab alissa]  
**Student ID**: [444050140]  
**Date Submitted**: [02/05/2026]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [[Paste your personal Gmail Google Drive link here](https://drive.google.com/file/d/1a_S0bpa9MTIW--ukyM7moAvTKmvuO7g4/view?usp=sharing)]

**Video filename**: `[YourStudentID]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [2026/04/29, 5:00pm]
**What I implemented**: 
Set up the repository, changed student ID, made first commit.- setup. Testing approach: Compiled and ran the original unsynchronized code to see the race con
ditions (inconsistent log counts)
**Challenges encountered**: 
Challenges encountered: None – straightforward 
**How I solved it**: 
Followed the VS Code cloning steps.
**Testing approach**: 
Compiled and ran the original unsynchronized code to see the race con
**Time spent**: 
30 min 
---

### Entry 2 - [2026/04/29, 7:00pm]
**What I implemented**: 
 Task 1 – fine-grained ReentrantLocks for the three counters.
**Challenges encountered**: 
 Understanding why fine-grained locking is better than a single 
**How I solved it**: 
Read about lock granularity; decided to use three separate locks becau
se the counters are independent.
**Testing approach**: 
Ran the program several times; counters now give the same values each run.
**Time spent**: 
1 hour
---

### Entry 3 - [2026/04/30, 5:00pm]
**What I implemented**: 
Task 2 – ReentrantLock for the execution log (ArrayList).
**Challenges encountered**: 
Initially forgot to unlock in finally block.
**How I solved it**: 
Initially forgot to unlock in finally block.
**Testing approach**: 
 made it to lock/unlock inside try finally.
**Time spent**: 
45mins
---

### Entry 4 - [2026/04/30, 6:30pm]
**What I implemented**: 
Task 3 – Semaphore for CPU control (binary semaphore with 1 permit).
**Challenges encountered**: 
Ensuring semaphore is also added to runToCompletion() method.- 
**How I solved it**: 
How I solved it: Wrapped both run() and runToCompletion() with acquire()/release() in f
inally.
**Testing approach**: 
Set Semaphore(2) temporarily to see concurrency effects; then reverte
d to 1.
**Time spent**: 
1 hour 
---

### Entry 5 - [2026/04/30, 7:30pm]
**What I implemented**: 
Completed ASSIGNMENT_DOCUMENTATION.md, recorded video, mad
e final commits.
**Challenges encountered**: 
Explaining lock granularity clearly.
**How I solved it**: 
 Drew a small diagram and wrote the explanation 
**Testing approach**: 
 Ran final code 5 times, all statistics identical
**Time spent**: 
 2:15 hours 
---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:
 **First race condition** – `contextSwitchCount++` (and the other counters).  
Shared resource: the integer counters.  
Problem: `++` is not atomic; two threads can read the same value, increment, and write 
back, causing a lost update.  
Incorrect behaviour: The final counter value is less than the actual number of incremen
ts.
- **Second race condition** – `executionLog.add(message)`.  
Shared resource: `ArrayList<String>`.  
Problem: `ArrayList` is not thread-safe; concurrent `add()` calls can corrupt internal 
structure, throw `ConcurrentModificationException`, or lose entries.  
Incorrect behaviour: Program may crash or log entries may disappear.
[Your answer here - 4-6 sentences with code examples]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:
- **ReentrantLock** is a mutual exclusion lock (binary). It guarantees that only one thre
ad holds the lock at a time. I used it for the counters and the log because those resourc
es require exclusive access.
- **Semaphore** maintains a set of permits. A binary semaphore (permits = 1) acts like a 
lock, but semaphores can also allow N concurrent accesses (e.g., a connection pool). I us
ed a `Semaphore(1)` to limit CPU execution – only one process can run at any moment, exac
tly matching a single-core CPU.
[Your answer here - explain your implementation choices]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:
- **Deadlock** occurs when two or more threads wait forever for each other’s locked resou
rces.
- Prevention techniques I used:
1. **Lock ordering** – I never acquire more than one lock at a time, so cyclic wait can
not happen.
2. **try-finally blocks** – Every `lock()` or `acquire()` is followed by a `finally` block
that releases the resource. This guarantees release even if an exception occurs, prev
enting resource leaks.- Additionally, the semaphore is acquired at the very beginning of the critical section a
nd released immediately after, so there is no nested locking.
[Your answer here - reference try-finally blocks, lock ordering, etc.]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:
- I chose **fine-grained locking** – three separate `ReentrantLock`s, one per counter (`c
ontextSwitchLock`, `completedProcessLock`, `waitingTimeLock`).- **Why:** The three counters are completely independent (updating one does not depend on 
the others). With a single coarse-grained lock, threads updating different counters would 
still block each other, creating unnecessary contention. Fine-grained locking allows true 
parallelism: while one thread increments `contextSwitchCount`, another can simultaneously 
increment `completedProcessCount`.- **Trade-offs:** Fine-grained requires more code and careful reasoning, but for independ
ent resources the concurrency gain is worth it. Coarse-grained is simpler but reduces thr
oughput.- Because the counters are independent, fine-grained locking provides **better concurrenc
y** – it exactly follows the principle: protect each shared resource with its own lock.
[Your answer here - explain coarse-grained vs fine-grained locking, independence of counters, concurrency implications. Show understanding of when to use each approach. 5-8 sentences expected.]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 
- Which variables: `contextSwitchCount`, `completedProcessCount`, `totalWaitingTime
**Why they need protection**: 
- The read-modify-write operations (increment, addition) are not atomic; 
without locks, updates can be lost.
**Synchronization mechanism used**: 
Three separate `ReentrantLock`s (fine-grained).
**Code snippet**:
```java
public static void incrementContextSwitch() {
contextSwitchLock.lock();
try { contextSwitchCount++; } finally { contextSwitchLock.unlock(); }
}
```

**Justification**: 
 Each counter is independent, so separate locks maximise concurrency
---

### Critical Section #2: Execution Log

**What resource**: 
- Resource: List<String> executionLog.
**Why it needs protection**: 
- ArrayList is not thread‑safe; concurrent add() calls cause
corruption or exceptions.
**Synchronization mechanism used**: 
 ReentrantLock logLock
**Code snippet**:
```java
public static void logExecution(String message) {
logLock.lock();
try { executionLog.add(message); } finally { logLock.unlock(); }
}
```

**Justification**: 
Exclusive access is required to preserve the logʼs integrity
---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 
Simulate a single‑core CPU – only one process can execute at a time.
**Number of permits and why**: 
 1 (binary semaphore)
 
**Where implemented**: 
Process.run() and Process.runToCompletion()
**Code snippet**:
```java
SharedResources.cpuSemaphore.acquire();
try {
// ... execution code ...
} finally {
SharedResources.cpuSemaphore.release();
}
```

**Effect on program behavior**: 

---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results
java SchedulerSimulationSync five times
**Testing procedure**: 
```bash
# Commands used (run the program at least 5 times)

javac SchedulerSimulationSync.java
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync

```

**Results**: 
(Show that running multiple times produces consistent, correct results)
all runs showed correct exact resaulted 
**Why synchronization is necessary**: 
(Explain what race conditions COULD occur without synchronization, even if you didn't observe them. Explain which shared resources need protection and why.)

**Conclusion**: 
Synchronization ensures correct behavior every run.
---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException
**Testing procedure**: 
- increase thread count
- re-enabled the lock
- remove the log lock temporarily to comfirm the exception happens 
**Results**: 
- Without locking → exception occurs

- With locking → no exceptions

What this proves:  
**What this proves**: 
The log lock is essential for thread‑safe logging.
---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: 
- total completed processes = number of processes

- Context switches = number of preemptions

- Total waiting time = sum of all waiting times
**Actual values**: 
All values matched expected results across multiple runs.
**Analysis**: 
Synchronization ensures correct, predictable scheduling behavior.
---

### Test 4: Different Scenarios
**Scenario tested**: [Changed time quantum and added more processes.]

**Purpose**: 
To verify that synchronization still works under different scheduling loads.
**Results**: 
- No race conditions

- No exceptions

- All counters remained consistent

**What I learned**: 
The synchronization design works well for multiple scheduling configurations.
---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[6-8 sentences about key concepts, challenges, insights]
i learned that synchronization is essential whenever multiple threads access shared resources. Even simple operations like ++ are unsafe without proper locking.
the difference between protecting data (using locks) and controlling access to a shared resource (using semaphores).
Understanding lock granularity helped me design a more efficient solution.
Overall, this assignment helped me understand real‑world concurrency problems and how to solve them.
---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 
Banking systems — multiple ATMs updating the same account balance must use synchronization to prevent incorrect withdrawals.
**Example 2**: 
Operating systems — the kernel must synchronize access to hardware devices, process tables, and memory.
---

### How I would explain synchronization to others:
Synchronization is like managing access to a bank account. imagine two ATMs trying to update the account at the same time , if both machines read the old balance at the same time :he account could lose money or show the wrong amount. 
Synchronization works like a “bank teller system” where only one ATM is allowed to update the account at a time.
A lock is like giving the ATM a special key — while it holds the key, no other ATM can modify the balance. Once it finishes, it returns the key so the next ATM can safely update the account.
This ensures that every transaction is processed correctly and nothing gets lost or corrupted.
[Explain to someone who just finished Assignment 1 - use simple terms and analogies]

---

## Part 6: GitHub Repository Information

**Repository URL**: 
https://github.com/ahmed-shehab-444/OS-Assignment3-Ahmed-Alissa.git 
**Number of commits**: 
7 
**Commit messages**: 
1. Refactor CPU semaphore handling in run method
2. Update SchedulerSimulationSync.java
3. Add locking mechanism to waiting time update
4. Add locking mechanism to increment completed process
5. Protect context switch counter with a lock
6. Implement synchronization in SharedResources class
7. Updated scheduler simulation 

---

## Summary

**Total time spent on assignment**: 
 5hours 30 mins 
**Key takeaways**: 
1. Synchronization is essential to prevent race conditions when multiple threads access shared data.
2. Fine‑grained locking improves concurrency when resources are independent.
3. Semaphores are useful for controlling access to shared hardware-like resources such as a CPU.

**Most challenging aspect**: 
Explaining lock granularity clearly and ensuring that every shared resource was properly protected without introducing deadlocks.
**What I'm most proud of**: 
Successfully implementing correct synchronization across counters, logs, and CPU execution — and achieving consistent, stable results across all test runs.
---

**End of Documentation**
