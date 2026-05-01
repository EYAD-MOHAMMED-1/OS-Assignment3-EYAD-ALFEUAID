# Assignment 3 - Complete Documentation

**Student Name**: [Eyad Mohammed Alfeuaid]  
**Student ID**: [445050233]  
**Date Submitted**: [Submission Date]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: https://drive.google.com/file/d/15b4wjf2g7bt1uIdZ-FLe6-F9kIB96W3q/view?usp=sharing

**Video filename**: `[445050233]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [May 1, 12:00 AM]
**What I implemented**: 
I found shared resources like counters and execution logs by examining the scheduler code that was supplied.
**Challenges encountered**: 
detecting the potential locations of race conditions in a multithreaded setting.
**How I solved it**: 
I went over every common variable and noted any crucial areas that needed synchronization.
**Testing approach**: 
ran the program several times to look for errors.
**Time spent**: 
1 hour
---

### Entry 2 - [May 1, 1:00 AM]
**What I implemented**: 
I implemented ReentrantLock to protect shared counters and execution log.
**Challenges encountered**: 
ensuring that lock() and unlock() are used correctly without creating deadlock.
**How I solved it**: 
Try-finally blocks were used to ensure lock release.
**Testing approach**: 
confirmed that no incorrect numbers showed up in the counters.
**Time spent**: 
30 minutes
---

### Entry 3 - [May 1, 1:30 AM]
**What I implemented**: 
To control CPU access, I added a semaphore.
**Challenges encountered**: 
deciding where acquire() and release() should be placed.
**How I solved it**: 
placed release in the finally block and acquire at the beginning of run().
**Testing approach**: 
verified that a single process is running at a time.
**Time spent**: 
30 minutes
---

### Entry 4 - [May 1, 2:00 AM]
**What I implemented**: 
I checked synchronization behavior and fully tested the program.
**Challenges encountered**: 
ensuring that there are no hidden race conditions.
**How I solved it**: 
repeated the process and compared the results.
**Testing approach**: 
ran the program more than five times.
**Time spent**: 
30 minutes
---

### Entry 5 - [May 1, 2:30 AM]
**What I implemented**: 
finished the documentation and checked the results.
**Challenges encountered**: 
developing clear explanations for synchronization.
**How I solved it**: 
reviewed lecture notes and ideas from the textbook.
**Testing approach**: 
final run of validation.
**Time spent**: 
1 hour
---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

The variable contextSwitchCount, which is increased by several threads, has one race situation. One update can be lost if two threads run contextSwitchCount++ simultaneously. The executionLog, an ArrayList, has another race problem. Concurrent additions may result in incorrect data or runtime problems since ArrayList is not thread-safe. Threads may override each other's operations in the absence of synchronization. Inaccurate statistics and incorrect execution logs result from this.

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

To ensure reciprocal exclusion when accessing shared data, ReentrantLock is utilized. It was utilized in this code to safeguard execution logs and counters. To manage access to limited resources, semaphore is utilized. To mimic CPU access, I employed a binary semaphore with a single permit. A single process can only run concurrently. Semaphores control resource access, whereas locks safeguard data consistency.

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

When threads wait indefinitely for resources owned by one another, deadlock occurs. Using try-finally blocks to ensure that locks are always released is one preventative strategy. Maintaining a consistent lock acquisition order is another way to prevent circular waits. Try-finally blocks were used in my code for both locks and semaphores to ensure release. This keeps threads from being blocked indefinitely.

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

To protect all three counters, I used a single lock (coarse-grained locking). This lowers the possibility of synchronization issues and streamlines the implementation. However, because only one thread may update any counter at once, it lowers concurrency. Performance would be enhanced by fine-grained locking, which would enable distinct locks for every counter. Fine-grained locking improves concurrency because the counters are independent. But for ease of use and dependability, I went for coarse-grained locking.

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 
contextSwitchCount, completedProcessCount, totalWaitingTime
**Why they need protection**: 
They are updated at the same time shared across threads.
**Synchronization mechanism used**: 
ReentrantLock
**Code snippet**:
```java
lock.lock();
try {
    contextSwitchCount++;
} finally {
    lock.unlock();
}
```

**Justification**: 
prevents race situations and ensures atomic updates.
---

### Critical Section #2: Execution Log

**What resource**: 
executionLog
**Why it needs protection**: 
The ArrayList is not thread-safe.
**Synchronization mechanism used**: 
ReentrantLock
**Code snippet**:
```java
lock.lock();
try {
    executionLog.add(message);
} finally {
    lock.unlock();
}
```

**Justification**: 
ensures safe concurrent access and stops data corruption
---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 
To control CPU access
**Number of permits and why**: 
One permit means that only one process is allowed out.
**Where implemented**: 
run() method
**Code snippet**:
```java
 try {
            SharedResources.cpuSemaphore.acquire();
        } catch (InterruptedException e1) {
            e1.printStackTrace();
        }
finally {
            
            SharedResources.cpuSemaphore.release();
        }
```

**Effect on program behavior**: 
prevents concurrent CPU access and ensures controlled execution.
---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: I examined whether running the program repeatedly under the same circumstances gives accurate and consistent results. I specifically concentrated on confirming that common variables, such contextSwitchCount, completedProcessCount, and totalWaitingTime, retain correct values independent of race conditions.

**Testing procedure**: 
```bash
javac SchedulerSimulationSync.java
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
```

**Results**: 
Every run returned consistent results with the right number of processes finished (18).

**Why synchronization is necessary**: 
Shared counters may generate inaccurate numbers in the absence of synchronization because of concurrent updating.

**Conclusion**: 
Synchronization ensures accuracy and uniformity.
---

### Test 2: Exception Testing
**What I tested**: When different threads access and change the shared executionLog (ArrayList) concurrently without appropriate synchronization, I examined whether the application produces a ConcurrentModificationException.

**Testing procedure**: 
ran the program several times to look for mistakes.
**Results**: 
There was no ConcurrentModificationException.
**What this proves**: 
The execution log is synchronized correctly.
---

### Test 3: Correctness Verification
**What I tested**: I tested whether the final computed values of the program are correct according to the scheduling logic. This includes verifying the correctness of:

Total number of completed processes
Context switch count
Total waiting time
Average waiting time
Individual process waiting times

**Expected values**: 
Processes completed = 18
**Actual values**: 
Processes completed = 18
**Analysis**: 
Correct synchronization can be detected by output that shows expected behavior.
---

### Test 4: Different Scenarios
**Scenario tested**: Different sequences for the execution of processes

**Purpose**: 
Check for robustness
**Results**: 
The program ran properly in every situation.
**What I learned**: 
Synchronization ensures dependability in many situations.
---

## Part 5: Reflection and Learning

### What I learned about synchronization:

This assignment taught me the need of synchronization in multithreaded systems to avoid race situations. I was understanding that safeguarding shared resources was necessary to prevent inaccurate outcomes. While semaphores regulate access to scarce resources like the CPU, ReentrantLock guarantees mutual exclusion. Concurrency problems are non-deterministic and might not show up in every run, I also discovered. It is crucial to recognize key areas and appropriately preserve them. Try-finally also guarantees that locks are always released, avoiding deadlocks.

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 
banking systems that allow several users to concurrently view and modify the same account balance. In the absence of synchronization, lost updates could result in inaccurate balances.
**Example 2**: 
Process scheduling in operating systems involves several programs contending for CPU time. The proper and equitable distribution of CPU resources is guaranteed through synchronization.
---

### How I would explain synchronization to others:

I would describe synchronization as a method of arranging many threads' access to shared resources. Consider a single restroom that is utilized by many different people, but only one person can use it at a time. A lock ensures that only one thread enters the crucial area, much like a key to the restroom. In the absence of synchronization, several threads may attempt to use the same resource at the same time, leading to disputes and incorrect results. In concurrent programming, synchronization ensures accuracy, safety, and order.

---

## Part 6: GitHub Repository Information

**Repository URL**: https://github.com/EYAD-MOHAMMED-1/OS-Assignment3-EYAD-ALFEUAID.git

**Number of commits**: 9

**Commit messages**: 
1. Set student ID
2. Add ReentrantLock for shared counters
3. Adding semaphore and import its package
4. Protecting shared variable name contextSwitchCount using lock
5. Protecting shared variable name completedProcessCount using lock
6. Protecting shared variable name totalWaitingTime using lock
7. Protecting shared variable name executionLog using lock
8. Use semaphore in process execution
9. Add synchronization to runToCompletion
---

## Summary

**Total time spent on assignment**: 4–5 hours

**Key takeaways**: 
1. In multithreaded programs, synchronization is crucial to avoiding race conditions.
2. Although they have separate functions, locks and semaphores cooperate to provide secure execution.
3. Correctness and performance are both enhanced by well-designed key parts.

**Most challenging aspect**: 
Finding every crucial piece and making sure that every shared resource was adequately safeguarded with no deadlocks was the most difficult part.
**What I'm most proud of**: 
My biggest achievement is successfully putting synchronization techniques into practice and making sure the program consistently produces accurate results over several runs.
---

**End of Documentation**
