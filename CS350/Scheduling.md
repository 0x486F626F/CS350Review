# Scheduling

```
a---s---t
response time = s - a
turnaround time = f - a
```

* Round-Robin
* First-Come-First-Serve
* Shortest-Job-First: minimizes ave turnaround time, long jobs may starve
* Shortest-Remaining-Time-First

### Typical Scheduler Objectives

* low response time 
* "fair" sharing of the CPU
* efficiency

CPU schedulers are often expected to consider process or thread priorities

* schedule the highest priority thread
* weighted fair sharing

### Multi-level Feedback Queues

* good responsiveness for interactive processes: threads of interactive processes block frequently, have short run times
* gradually diminish priority of threads with long run times and infrequent blocking
	* if a thread blocks before its quantum is used up, raise its priority
	* if a thread uses its entire quantum, lower its priority

#### Algorithm

* scheduler maintains several round-robin ready queues: Q_0 -> Q_n high -> low
* scheduler always chooses thread from the lower non-empty queue
* lower priority has longer quantum
* new threads go into ready queue with highest priority
* preempted thread lowers priority

## Linux Complete Fair Scheduler (CFS)

* a weighted fair sharing approach

## Contention and Scalability

* access to shared ready queue need mutual exclusion
* as number of cores grows, contention for ready queue becomes a problem
* per core design scales to a larger number of cores

## CPU cache affinity

* as thread runs, accessed data is loaded into CPU cache
* moving the thread to another core means data must be reloaded into that core's caches
* as thread runs, it acquires an affinity for one core because of the cached data
* per core design benefits from affinity by keeping threads on the same core
* shared queue design does not

## Load Balancing

In per-core design, cores may be idle while others are busy.
Typically need some mechanism for thread migration to address load imbalances
