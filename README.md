<div align="center">

# Multithreaded Programming Using Pthreads and OpenMP

### Thread Creation, Work Distribution, Synchronization, Coordination, and Performance Analysis

</div>

---

## Table of Contents

- [1. Overview](#1-overview)
- [2. Aim](#2-aim)
- [3. Objectives](#3-objectives)
- [4. Software Environment](#4-software-environment)
- [5. Concepts Covered](#5-concepts-covered)
- [6. Repository Program List](#6-repository-program-list)
- [7. Setup and Compilation](#7-setup-and-compilation)
- [8. Part A — Pthreads Experiments](#8-part-a--pthreads-experiments)
- [9. Part B — OpenMP Experiments](#9-part-b--openmp-experiments)
- [10. Part C — Performance Analysis](#10-part-c--performance-analysis)
- [11. Performance Results](#11-performance-results)
- [12. Graphical Analysis](#12-graphical-analysis)
- [13. Pthreads vs OpenMP](#13-pthreads-vs-openmp)
- [14. Troubleshooting](#14-troubleshooting)
- [15. Conclusion](#15-conclusion)

---

## 1. Overview

This lab explores multithreaded programming in C using **Pthreads (POSIX Threads)** and **OpenMP**. It begins with creating and managing threads, then moves to dividing work among threads, identifying race conditions, protecting shared data, and coordinating parallel execution. The final section compares sequential, Pthreads, and OpenMP execution for a computational workload.

The documented environment is Windows with WSL Ubuntu, GCC, Pthreads, OpenMP, and the Nano editor. The performance tests use a workload of `N = 1,000,000,000` loop iterations. The toolchain and experiment environment are specified in the supplied lab document. fileciteturn1file0L54-L61

## 2. Aim

To develop multithreaded programs using Pthreads and OpenMP and understand thread creation, thread management, work distribution, race conditions, synchronization, thread coordination, and performance improvement through multiple threads. fileciteturn1file0L9-L17

## 3. Objectives

- Create and manage one or more threads.
- Understand how the main thread and additional threads execute.
- Divide a computational task among threads.
- Observe why thread output order is not guaranteed.
- Demonstrate a race condition caused by concurrent updates to shared data.
- Protect shared data using a Pthreads mutex and an OpenMP critical section.
- Coordinate threads using an OpenMP barrier.
- Use OpenMP work-sharing and reduction to combine partial results.
- Compare sequential, Pthreads, and OpenMP execution time.
- Calculate speedup and efficiency and interpret the resulting graphs.

## 4. Software Environment

| Component | Purpose |
|---|---|
| Windows | Host operating system |
| WSL Ubuntu | Linux environment for creating and executing C programs |
| GCC | C compiler |
| Pthreads | Explicit thread creation, joining, and mutex synchronization |
| OpenMP | Directive-based shared-memory parallel programming |
| Nano | Terminal-based source-code editor |

These tools and environments are listed in the supplied lab document. fileciteturn1file0L54-L61

### Initial environment setup

Open PowerShell and enter WSL:

```powershell
wsl
```

In the Ubuntu terminal, create the lab directory and enter it:

```bash
mkdir -p ~/parallel_lab
cd ~/parallel_lab
pwd
```

Check GCC and OpenMP compiler support:

```bash
gcc --version
gcc -fopenmp --version
```

If GCC is not installed, install the build tools in Ubuntu:

```bash
sudo apt update
sudo apt install build-essential -y
```

## 5. Concepts Covered

| Concept | Definition |
|---|---|
| **Thread** | A path of execution within a program. |
| **Main thread** | The thread that begins by executing `main()`. |
| **Additional thread** | A thread created by the program, for example using `pthread_create()`. |
| **Multithreading** | Using multiple threads within one program. |
| **Parallel programming** | Dividing work so multiple execution units can perform parts of it concurrently. |
| **Work distribution** | Splitting a large task into smaller tasks and assigning them to threads. |
| **Race condition** | Concurrent access or modification of shared data that can produce an incorrect result. |
| **Mutex** | A locking mechanism used to protect a critical section in Pthreads. |
| **Critical section** | A code section in which simultaneous access must be restricted. |
| **Barrier** | A synchronization point where threads wait until all required threads arrive. |
| **Speedup** | Sequential execution time divided by parallel execution time. |
| **Efficiency** | Speedup divided by the number of threads, expressed as a percentage. |

These definitions follow the terminology used in the lab document. fileciteturn1file0L1709-L1749

## 6. Repository Program List

The source document identifies the following programs for the experiment. fileciteturn1file0L1752-L1781

| Part | Source file | Experiment / purpose |
|---|---|---|
| Pthreads | `thread1.c` | Create one additional thread |
| Pthreads | `thread2.c` | Create multiple threads |
| Pthreads | `thread_sum.c` | Distribute array-sum work among threads |
| Pthreads | `race.c` | Demonstrate a race condition |
| Pthreads | `mutex.c` | Fix the race condition using a mutex |
| Pthreads | `pthread_perf.c` | Measure performance with different thread counts |
| OpenMP | `omp1.c` | Parallel region and thread identification |
| OpenMP | `omp_sum.c` | Work sharing and reduction |
| OpenMP | `omp_race.c` | Demonstrate a race condition |
| OpenMP | `omp_critical.c` | Synchronization using a critical section |
| OpenMP | `omp_barrier.c` | Thread coordination |
| OpenMP | `omp_perf.c` | Measure OpenMP performance |
| Performance | `sequential.c` | Establish the sequential baseline |

## 7. Setup and Compilation

Create each source file in the lab directory using Nano. For example:

```bash
nano thread1.c
```

Paste the corresponding program from the lab document, then save using **Ctrl+O**, press **Enter**, and exit with **Ctrl+X**.

### Compilation command reference

| Experiment | Compile command |
|---|---|
| One Pthreads thread | `gcc thread1.c -o thread1 -pthread` |
| Multiple Pthreads threads | `gcc thread2.c -o thread2 -pthread` |
| Pthreads array sum | `gcc thread_sum.c -o thread_sum -pthread` |
| Pthreads race condition | `gcc race.c -o race -pthread` |
| Pthreads mutex | `gcc mutex.c -o mutex -pthread` |
| OpenMP parallel region | `gcc omp1.c -o omp1 -fopenmp` |
| OpenMP array sum | `gcc omp_sum.c -o omp_sum -fopenmp` |
| OpenMP race condition | `gcc omp_race.c -o omp_race -fopenmp` |
| OpenMP critical section | `gcc omp_critical.c -o omp_critical -fopenmp` |
| OpenMP barrier | `gcc omp_barrier.c -o omp_barrier -fopenmp` |
| Sequential benchmark | `gcc sequential.c -o sequential` |
| Pthreads benchmark | `gcc pthread_perf.c -o pthread_perf -pthread` |
| OpenMP benchmark | `gcc omp_perf.c -o omp_perf -fopenmp` |

Run a compiled program using `./program_name`, replacing `program_name` with the executable name.

---

## 8. Part A — Pthreads Experiments

Pthreads stands for **POSIX Threads**. It gives the programmer explicit control over thread creation, waiting for thread completion, and mutex-based synchronization. The lab uses `pthread_create()`, `pthread_join()`, `pthread_mutex_lock()`, and `pthread_mutex_unlock()`. fileciteturn1file0L134-L140

### Experiment A1 — Create one thread

**File:** `thread1.c`

**Objective:** Create an additional thread, execute a function in it, and wait for it to finish.

**Key functions**
- `pthread_create()` creates the additional thread.
- `thread_function()` contains the work performed by that thread.
- `pthread_join()` makes the main thread wait for the created thread.

**Compile and run**

```bash
gcc thread1.c -o thread1 -pthread
./thread1
```

**Expected output**

```text
Hello from the thread!
Main thread finished.
```

**Result:** One additional thread is created alongside the original main thread. The main thread waits for it using `pthread_join()`. fileciteturn1file0L146-L279

### Experiment A2 — Create multiple threads

**File:** `thread2.c`

**Objective:** Create four threads and observe that the operating system determines their execution order.

**Compile and run**

```bash
gcc thread2.c -o thread2 -pthread
./thread2
```

**Expected output pattern**

```text
Hello from Thread 1
Hello from Thread 2
Hello from Thread 4
Hello from Thread 3
All threads have finished.
```

The thread-message order may differ on each run. The important point is that all four threads finish before the final message is printed.

**Result:** Multiple threads can execute concurrently, and their output order is not guaranteed. fileciteturn1file0L284-L391

### Experiment A3 — Divide work among threads

**File:** `thread_sum.c`

**Objective:** Divide an array into four parts, calculate partial sums in separate threads, and combine the results.

**Input array:** `[10, 20, 30, 40, 50, 60, 70, 80]`

| Thread | Elements | Partial sum |
|---|---|---:|
| Thread 1 | 10 + 20 | 30 |
| Thread 2 | 30 + 40 | 70 |
| Thread 3 | 50 + 60 | 110 |
| Thread 4 | 70 + 80 | 150 |
| **Total** | All elements | **360** |

**Compile and run**

```bash
gcc thread_sum.c -o thread_sum -pthread
./thread_sum
```

**Expected final output**

```text
Total sum = 360
```

**Result:** Work distribution splits a larger computation into smaller pieces. Each thread calculates a partial result, and the main thread combines the partial sums after joining the threads. fileciteturn1file0L394-L515

### Experiment A4 — Demonstrate a race condition

**File:** `race.c`

**Objective:** Observe what happens when multiple threads update the same shared variable without synchronization.

The program uses four threads, each performing 100,000 increments of `counter`.

**Compile and run**

```bash
gcc race.c -o race -pthread
./race
```

**Expected output pattern**

```text
Expected counter = 400000
Actual counter = <a potentially incorrect value>
```

The actual value may vary between runs and can be less than 400,000 because concurrent read-modify-write operations can overwrite one another.

**Result:** A race condition occurs when threads modify shared data without proper coordination. fileciteturn1file0L521-L628

### Experiment A5 — Fix the race condition using a mutex

**File:** `mutex.c`

**Objective:** Protect the shared counter with a mutex so that only one thread at a time performs the increment.

**Key operations**

```c
pthread_mutex_lock(&mutex);
counter++;
pthread_mutex_unlock(&mutex);
```

**Compile and run**

```bash
gcc mutex.c -o mutex -pthread
./mutex
```

**Expected output**

```text
Expected counter = 400000
Actual counter = 400000
```

**Result:** A mutex protects the critical section and prevents simultaneous updates to the shared counter. fileciteturn1file0L631-L731

---

## 9. Part B — OpenMP Experiments

OpenMP is a directive-based shared-memory parallel programming model. Instead of manually creating every thread, the programmer uses directives such as `#pragma omp parallel`, `#pragma omp parallel for`, `#pragma omp critical`, and `#pragma omp barrier`. fileciteturn1file0L739-L749

### Experiment B1 — Basic parallel region

**File:** `omp1.c`

**Objective:** Create an OpenMP team of threads and identify each thread.

**Compile and run**

```bash
gcc omp1.c -o omp1 -fopenmp
./omp1
```

**Important functions**
- `omp_get_thread_num()` returns the current thread's ID.
- `omp_get_num_threads()` returns the number of threads in the current parallel team.

The lab reports that the environment used 32 threads for this example. The actual number depends on the environment and runtime configuration.

**Result:** OpenMP creates and manages the parallel team for the parallel region. Output order is not guaranteed. fileciteturn1file0L752-L826

### Experiment B2 — Work sharing and reduction

**File:** `omp_sum.c`

**Objective:** Divide array-processing loop iterations among OpenMP threads and combine partial sums safely.

**Key directive**

```c
#pragma omp parallel for reduction(+:total_sum)
```

The `parallel for` directive distributes loop iterations among the available threads. The `reduction(+:total_sum)` clause combines thread-local partial sums into the final total.

**Compile and run**

```bash
gcc omp_sum.c -o omp_sum -fopenmp
./omp_sum
```

**Expected final output**

```text
Total sum = 360
```

**Result:** OpenMP simplifies loop work distribution and safely combines partial results using reduction. fileciteturn1file0L830-L923

### Experiment B3 — OpenMP race condition

**File:** `omp_race.c`

**Objective:** Demonstrate that shared-data updates can still race even when OpenMP manages the threads.

The program creates four threads, each incrementing the shared counter 100,000 times, without protecting the increment.

**Compile and run**

```bash
gcc omp_race.c -o omp_race -fopenmp
./omp_race
```

**Expected output pattern**

```text
Expected counter = 400000
Actual counter = <a potentially incorrect value>
```

The actual value is timing-dependent. The lab gives `100000` as an example, but other incorrect values are possible.

**Result:** OpenMP does not automatically make every shared-data operation safe. Synchronization is still needed when multiple threads modify shared state. fileciteturn1file0L927-L990

### Experiment B4 — Fix the race using `critical`

**File:** `omp_critical.c`

**Objective:** Protect the shared counter using an OpenMP critical section.

**Key directive**

```c
#pragma omp critical
{
    counter++;
}
```

**Compile and run**

```bash
gcc omp_critical.c -o omp_critical -fopenmp
./omp_critical
```

**Expected output**

```text
Expected counter = 400000
Actual counter = 400000
```

**Result:** The critical section permits only one thread at a time to execute the protected block. fileciteturn1file0L993-L1071

### Experiment B5 — Thread coordination using a barrier

**File:** `omp_barrier.c`

**Objective:** Ensure all threads complete Stage 1 before any thread begins Stage 2.

**Key directive**

```c
#pragma omp barrier
```

**Compile and run**

```bash
gcc omp_barrier.c -o omp_barrier -fopenmp
./omp_barrier
```

**Expected output pattern**

All `completed Stage 1` messages appear before any `started Stage 2` message. The order of messages within each stage may differ.

**Result:** A barrier acts as a synchronization point, ensuring that every thread reaches it before any thread continues beyond it. fileciteturn1file0L1077-L1147

---

## 10. Part C — Performance Analysis

The performance experiment runs the same numerical workload in three forms: sequential C, Pthreads, and OpenMP. The workload is `N = 1,000,000,000` iterations and accumulates `sum += (double)i * 0.000001`. The expected result is `499999999500.00`. fileciteturn1file0L1155-L1161

### Experiment C1 — Sequential baseline

**File:** `sequential.c`

**Compile and run**

```bash
gcc sequential.c -o sequential
./sequential
```

The document records five measured runs: `1.353895`, `1.355794`, `1.349621`, `1.353422`, and `1.353365` seconds.

| Metric | Recorded result |
|---|---:|
| Iterations | 1,000,000,000 |
| Average baseline time | 1.353219 seconds |
| Result | 499999999500.00 |

The average sequential time is the reference value for calculating speedup. fileciteturn1file0L1163-L1233

### Experiment C2 — Pthreads performance

**File:** `pthread_perf.c`

**Compile and run**

```bash
gcc pthread_perf.c -o pthread_perf -pthread
./pthread_perf
```

Enter the requested thread count when prompted. The documented tests use 1, 2, 4, 6, and 16 threads.

| Number of threads | Execution time (seconds) |
|---:|---:|
| 1 | 1.348142 |
| 2 | 0.680737 |
| 4 | 0.358872 |
| 6 | 0.241345 |
| 16 | 0.144812 |

**Observation:** The recorded execution time decreases as the number of Pthreads increases for this workload. fileciteturn1file0L1240-L1419

### Experiment C3 — OpenMP performance

**File:** `omp_perf.c`

**Compile and run**

```bash
gcc omp_perf.c -o omp_perf -fopenmp
./omp_perf
```

Enter the requested thread count when prompted. The documented tests use 1, 2, 4, 6, and 16 threads.

| Number of threads | Execution time (seconds) |
|---:|---:|
| 1 | 1.409294 |
| 2 | 0.715560 |
| 4 | 0.360803 |
| 6 | 0.241608 |
| 16 | 0.140692 |

**Observation:** OpenMP also reduces execution time as the thread count increases in the recorded workload. At 16 threads, the recorded OpenMP time is slightly lower than the Pthreads time. fileciteturn1file0L1427-L1535

---

## 11. Performance Results

### 11.1 Execution-time comparison

| Threads | Pthreads time (s) | OpenMP time (s) |
|---:|---:|---:|
| 1 | 1.348142 | 1.409294 |
| 2 | 0.680737 | 0.715560 |
| 4 | 0.358872 | 0.360803 |
| 6 | 0.241345 | 0.241608 |
| 16 | 0.144812 | 0.140692 |

The values above are the recorded results in the lab document. fileciteturn1file0L1538-L1566

### 11.2 Speedup

Speedup indicates how much faster a parallel run is compared with the sequential baseline.

\[
\text{Speedup} = \frac{\text{Sequential baseline time}}{\text{Parallel execution time}}
\]

The source document uses a sequential baseline of `1.353219` seconds.

| Threads | Pthreads speedup | OpenMP speedup |
|---:|---:|---:|
| 1 | 1.004× | 0.960× |
| 2 | 1.988× | 1.891× |
| 4 | 3.771× | 3.751× |
| 6 | 5.608× | 5.601× |
| 16 | 9.345× | 9.618× |

At 16 threads, the reported speedups are approximately **9.35× for Pthreads** and **9.62× for OpenMP**. fileciteturn1file0L1571-L1611

### 11.3 Efficiency

Efficiency describes the measured speedup relative to the number of threads.

\[
\text{Efficiency (\%)} = \frac{\text{Speedup}}{\text{Number of threads}} \times 100
\]

| Threads | Pthreads efficiency | OpenMP efficiency |
|---:|---:|---:|
| 1 | 100.38% | 96.02% |
| 2 | 99.39% | 94.56% |
| 4 | 94.27% | 93.76% |
| 6 | 93.45% | 93.35% |
| 16 | 58.40% | 60.11% |

The reported efficiency decreases at 16 threads, showing that speedup is not directly proportional to thread count. Small differences in measured timing and the baseline can also cause efficiency values slightly above 100% at low thread counts. fileciteturn1file0L1615-L1652

## 12. Graphical Analysis

### Figure 1 — Execution time vs number of threads

![Execution time comparison for Pthreads and OpenMP](execution_time.png)

Both implementations generally take less time as more threads are used. At 16 threads, OpenMP has the lowest recorded execution time in this data set.

### Figure 2 — Speedup vs number of threads

![Speedup comparison for Pthreads and OpenMP](speedup.png)

Speedup rises with thread count in the recorded tests. At 16 threads, OpenMP reaches approximately 9.62× speedup, while Pthreads reaches approximately 9.35×.

### Figure 3 — Efficiency vs number of threads

![Efficiency comparison for Pthreads and OpenMP](efficiency.png)

Efficiency is relatively high at the lower tested thread counts and drops at 16 threads. Additional threads still improve execution time, but they do not produce a proportional increase in speedup.

The three graphs correspond to the performance analysis described in the supplied document. fileciteturn1file0L1554-L1652

### Why 16 threads do not produce 16× speedup

The document estimates idealized time by dividing the sequential baseline by 16:

\[
1.35 / 16 \approx 0.084 \text{ seconds}
\]

The measured 16-thread OpenMP time is `0.140692` seconds. Real parallel programs incur overhead from thread management, scheduling, synchronization, memory access, operating-system activity, and work that cannot be parallelized. Therefore, ideal linear speedup is not generally achieved. fileciteturn1file0L1655-L1679

## 13. Pthreads vs OpenMP

| Concept | Pthreads | OpenMP |
|---|---|---|
| Thread creation | Explicit `pthread_create()` calls | Directives such as `#pragma omp parallel` |
| Wait for completion | `pthread_join()` | Threads complete the parallel region before execution proceeds beyond it |
| Work distribution | Programmer explicitly assigns work | `parallel for` can distribute loop iterations |
| Shared-data protection | Mutex | `critical` |
| Coordination | Join and synchronization mechanisms | Barrier |
| Combining partial results | Programmer-managed | `reduction` clause |

Pthreads offers lower-level, explicit control. OpenMP offers a higher-level approach for parallel regions and loop-based work sharing. Both require correct synchronization when threads access shared mutable data. fileciteturn1file0L1682-L1702

## 14. Troubleshooting

| Issue | What to check |
|---|---|
| `wsl` does not open Ubuntu | Confirm WSL and the Ubuntu distribution are installed and available. |
| `gcc: command not found` | Install `build-essential` inside Ubuntu. |
| Pthreads compilation fails | Include `-pthread` in the compilation command. |
| OpenMP compilation fails | Include `-fopenmp` in the GCC command. |
| Output order changes between runs | This is normal for concurrently executing threads. |
| Counter is smaller than expected in a race-condition test | This is the intended demonstration of unsynchronized shared updates. |
| Mutex/critical version still reports a wrong result | Check that the lock is initialized, the protected increment is inside the critical section, and all threads are joined before printing. |
| OpenMP uses a different number of threads | Runtime defaults and system resources can affect the team size; use `omp_set_num_threads()` where specified in the experiment. |
| Execution time differs from the document | Timing depends on hardware, system load, virtualization, compiler settings, and runtime behavior. |

## 15. Conclusion

This experiment demonstrates multithreaded programming using Pthreads and OpenMP, beginning with thread creation and management and progressing to work distribution, race conditions, synchronization, and coordination. Pthreads uses explicit thread-management functions and mutexes, while OpenMP uses directives for parallel regions, loop work sharing, critical sections, barriers, and reductions. fileciteturn1file0L1860-L1870

The performance tests show that execution time generally decreases as the number of threads increases. With 16 threads, the recorded Pthreads time is `0.144812` seconds and the OpenMP time is `0.140692` seconds. The corresponding reported speedups are approximately `9.35×` and `9.62×`. Efficiency also demonstrates that increasing thread count does not guarantee proportional speedup because parallel execution introduces overhead. fileciteturn1file0L1877-L1894

**Overall learning flow:** Create threads → Manage threads → Distribute work → Identify race conditions → Synchronize shared data → Coordinate threads → Measure execution time → Analyze speedup and efficiency.

---

### Notes

- Performance values are the measurements reported in the supplied lab document; they are not newly benchmarked here.
- Keep `README.md` and the three graph images in the same directory for the image references to work on GitHub.
- The charts use distinct series styling for Pthreads and OpenMP.
