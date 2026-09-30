# Multithreaded Programming Using Pthreads and OpenMP

## 1. Project Overview

This experiment demonstrates multithreaded programming in C using:

- **POSIX Threads (Pthreads)**
- **OpenMP**
- **Sequential execution** for performance comparison

The experiment covers thread creation and management, parallel computation, race conditions, synchronization mechanisms, and performance analysis using execution time, speedup, and efficiency.

## 2. Objectives

- Understand thread creation and management.
- Implement parallel programs using Pthreads.
- Implement parallel programs using OpenMP.
- Understand race conditions and synchronization.
- Use mutexes, critical sections, reduction, and barriers.
- Compare sequential and parallel execution.
- Calculate speedup and efficiency.

## 3. Technologies Used

| Technology | Purpose |
|---|---|
| C | Programming language |
| GCC | C compiler |
| POSIX Threads (Pthreads) | Thread-based parallel programming |
| OpenMP | Shared-memory parallel programming |
| Ubuntu / WSL 2 | Development and execution environment |

## 4. Repository Structure

### Pthreads

| File | Description |
|---|---|
| `thread1.c` | Basic thread creation |
| `thread2.c` | Multiple threads |
| `thread_sum.c` | Parallel summation using threads |
| `race.c` | Demonstrates a race condition |
| `mutex.c` | Resolves a race condition using a mutex |
| `pthread_perf.c` | Measures Pthreads performance |

### OpenMP

| File | Description |
|---|---|
| `omp1.c` | Basic OpenMP parallel execution |
| `omp_sum.c` | Parallel summation/reduction |
| `omp_race.c` | Demonstrates a race condition |
| `omp_critical.c` | Synchronization using a critical section |
| `omp_barrier.c` | Demonstrates barrier synchronization |
| `omp_perf.c` | Measures OpenMP performance |

### Sequential

| File | Description |
|---|---|
| `sequential.c` | Sequential baseline program used for performance comparison |

Existing folders in the repository are retained as part of the project structure.

## 5. How to Compile and Run

The following commands can be executed in **Ubuntu / WSL 2**.

### Pthreads Examples

```bash
gcc thread1.c -o thread1 -pthread
./thread1
```

```bash
gcc thread2.c -o thread2 -pthread
./thread2
```

```bash
gcc thread_sum.c -o thread_sum -pthread
./thread_sum
```

```bash
gcc race.c -o race -pthread
./race
```

```bash
gcc mutex.c -o mutex -pthread
./mutex
```

```bash
gcc pthread_perf.c -o pthread_perf -pthread
./pthread_perf
```

### OpenMP Examples

```bash
gcc omp1.c -o omp1 -fopenmp
./omp1
```

```bash
gcc omp_sum.c -o omp_sum -fopenmp
./omp_sum
```

```bash
gcc omp_race.c -o omp_race -fopenmp
./omp_race
```

```bash
gcc omp_critical.c -o omp_critical -fopenmp
./omp_critical
```

```bash
gcc omp_barrier.c -o omp_barrier -fopenmp
./omp_barrier
```

```bash
gcc omp_perf.c -o omp_perf -fopenmp
./omp_perf
```

### Sequential Program

```bash
gcc sequential.c -o sequential_run
./sequential_run
```

The executable is named `sequential_run` because `sequential` may already be used as a directory in the repository/environment.

## 6. Performance Experiment

The performance programs were tested using:

- 1 thread
- 2 threads
- 4 threads
- 6 threads
- 16 threads

The sequential program was used as the baseline for comparison.

### Measured Execution Times

| Number of Threads | Pthreads Time (s) | OpenMP Time (s) |
|---:|---:|---:|
| 1 | 1.895522 | 1.860159 |
| 2 | 0.989448 | 1.008585 |
| 4 | 0.663242 | 0.650145 |
| 6 | 0.513231 | 0.490406 |
| 16 | 0.341446 | 0.588484 |

**Sequential baseline:** `1.961537 seconds`

## 7. Speedup and Efficiency

### Speedup

**Speedup = Sequential Time / Parallel Time**

### Efficiency

**Efficiency = (Speedup / Number of Threads) × 100**

| Threads | Pthreads Time | Pthreads Speedup | Pthreads Efficiency | OpenMP Time | OpenMP Speedup | OpenMP Efficiency |
|---:|---:|---:|---:|---:|---:|---:|
| 1 | 1.895522 | 1.035× | 103.48% | 1.860159 | 1.055× | 105.45% |
| 2 | 0.989448 | 1.983× | 99.12% | 1.008585 | 1.945× | 97.24% |
| 4 | 0.663242 | 2.958× | 73.94% | 0.650145 | 3.017× | 75.43% |
| 6 | 0.513231 | 3.822× | 63.70% | 0.490406 | 4.000× | 66.66% |
| 16 | 0.341446 | 5.745× | 35.90% | 0.588484 | 3.333× | 20.83% |

> The efficiency values are calculated from the measured execution times and the specified formulas. Values above 100% at one thread reflect measurement variation relative to the sequential baseline.

## 8. Observations

- Parallel execution reduces execution time compared with the sequential baseline.
- Pthreads execution time continued to decrease up to 16 threads in this experiment.
- OpenMP execution time decreased up to 6 threads.
- The OpenMP 16-thread execution was slower than the 6-thread execution in this particular experiment.
- Increasing the number of threads does not always produce proportional speedup.
- Thread-management overhead, synchronization, CPU resource limitations, and system load can affect performance.
- The results are specific to the hardware and execution environment used during the experiment.

## 9. Conclusion

This experiment successfully demonstrated multithreaded programming using **Pthreads** and **OpenMP**.

The experiment covered:

- Pthreads
- OpenMP
- Race conditions
- Synchronization
- Mutexes
- Critical sections
- Barrier synchronization
- Parallel summation/reduction
- Performance comparison
- Speedup
- Efficiency

The measured results show that increasing the number of threads can reduce execution time, but the improvement is not always proportional. Performance also depends on the execution environment and system resources. The results obtained in this experiment are specific to the tested hardware and environment and do not imply that one framework is universally better than the other.

## 10. Author / Academic Information

- **Course/Laboratory:** Parallel Computing / Multithreaded Programming
- **Platform:** Ubuntu on WSL 2
- **Language:** C
