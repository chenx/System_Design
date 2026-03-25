# Concurrency and parallelism in Java

## Concurrency and parallelism in Java

https://medium.com/@peterlee2068/concurrency-and-parallelism-in-java-f625bc9b0ca4

### Unit of Concurrency

Concurrency has three levels:
- Multiprocessing: Multiple Processors/CPUs executing concurrently. This unit here is a CPU.
- Multitasking: Multiple tasks/processes running concurrently on a single CPU. The OS executes these tasks by switching between them very frequently. This unit here is a Process.
- Multithreading: Multiple parts of the same program running concurrently. In this case, we go a step further and divide the same program into multiple parts/threads and run those threads concurrently.

### Process vs Thread

A **process** is a program in execution. It has its own address space, a call stack, and link to any resources such as open files. A computer system normally has multiple processes running at a time. The OS keeps track of all these processes and facilitates their execution by sharing the processing time of the CPU among them.

A **thread** is a path of execution within a process. Every process has at least one thread called the main thread. The main thread can create additional threads within the process. Threads within a process share the process’s resources including memory and open files. Besides, every thread has its own call stack which will be created at runtime. For every method call, one entry will be added in the stack called a stack frame. Each stack frame has the reference for the local variable array, operand stack, and runtime constant pool of a class where the method being executed belongs.

### Thread in Java

- Option 1: You can create a class that inherits the Thread.java class.
- Option 2: You can use a Runnable object.

Pause a Thread via sleep() method and waiting for the completion of another thread via the join() method. 

<br/>

To create and manage Thread in Java you can use the Executors framework. Java Concurrency API defines three executor interfaces that cover everything that is needed for creating and managing threads:

- Executor: launch a task specified by a Runnable object.
- ExecutorService: a sub-interface of Executor that adds functionality to manage the lifecycle of the tasks.
- ScheduledExecutor: a sub-interface of ExecutorService that adds functionality to schedule the execution of the tasks.

<br/>

Thread has 3 **priorities**: Thread.MIN_PRIORITY (1), Thread.NORM_PRIORITY(5), Thread.MAX_PRIORITY(10). The default thread has priority: Thread.NORM_PRIORITY.

**Synchronization & Locks**: In a multiple-thread program, access to shared variables must be synchronized in order to prevent race conditions. We can use the **synchronized** keyword for method or block. Besides, you can use the **volatile** keyword to avoid memory consistency errors in multi-thread programs. If you mark a variable as volatile the compiler won’t optimize or reorder instructions around that variable.

Instead of using an intrinsic lock via the synchronized keyword, you can also use various **Locking classes** provided by Java Concurrency API: ReentrantLock(since java 1.5), ReadWriteLock(since java 1.5), StampedLock(since java 1.8) and **Atomic Variables** (since java 1.5): AtomicInteger, AtomicBoolean, AtomicLong, AtomicReference and so on. More details: https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/atomic/package-summary.html

<br/>

## Parallel Programming in java

https://www.geeksforgeeks.org/advance-java/parallel-programming-in-java/

The Fork/Join Framework

The Fork/Join option introduced in Java 7 is designed for work that can be repeatedly split into smaller chunks. It uses a collection of worker threads to steal jobs from each other, increasing CPU utilization. The framework follows a divide-and-conquer strategy:

- Fork: Divide the project into smaller subtasks.
- Join: Create subtasks and add them to the results.

    
