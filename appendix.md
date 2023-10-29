# Appendix A

Task Specs include all configuration necessary to run a task.
Task Service retrieves list of jobs from Job Store to generate Task Specs.  
Task manager fetches Task Specs from Task Service.  
Turbine Containers run a Task Manager which spawns stream processing tasks in
the container.  
Tasks are scheduled by the Task Manager and reshuffled across Turbine
Containers by the Shard Manager depending on work load (every 30m):
1. Shard Manager: Request(0)=DROP_SHARD
2. Task Manager0: Stops associated task. Removes shard. Return=SUCCESS
3. Shard Manager: Update {Shard -> Container} mapping. Request(1)=ADD_SHARD
4. Task Manager1: Add shard. retrieve list of tasks. Start associated tasks.

# Appendix B

How to define shard load?
small task: dynamic resource usage (e.g., average memory).
large task: peak resource usage (e.g., cgroup limits).

# Appendix C

How to detect symptoms?
- lag (time_lagged = total_bytes_lagged/processing_rate) (1),
- imbalanced input (standard deviation of processing rate of tasks per job),
- OOM (metric collection of cgroup stats after tasks are killed),
Problems (too little information):
- May take too long for a job to converge.
- May downscale a healthy job to become unhealthy.
- May make bad scaling decisions.

# Appendix D

How to estimate resource utilization?
Stateless jobs:
CPU resource estimates:
    - X/(P * k * n) (2)
    where:
        X: input rate
        P: processing rate per thread
        k: number of threads per task
        n: number of parallel tasks
    - (X + B/t)/(P * k * n) (3)
    where:
        B: backlogged data
        t: time
Stateful jobs:
    - Also estimate memory and disk usage

# Appendix E

how to make sure the final plan has enough resources?
- prevent downscaling of a healthy job to become unhealthy.
- ensure that untriaged problems do not trigger unnecessary scaling decisions.
- execute necessary adjustments for multiple resources in a correlated manner.
  i.e., if a stateful job is bottlenecked on CPU, and the number of tasks is
  increased, the memory allocated to each task can be reduced.

# Appendix F

n' = ceil(X/P) (4)
    where:
    n': new task count
    X : input rate
    P : processing rate per thread (estimate)
n' > n => P_{i+1} = X/n (estimate P is too low, adjust)
n' < n => n_{i+1} = n'  (reduce the number of jobs, monitor for SLO violations)

