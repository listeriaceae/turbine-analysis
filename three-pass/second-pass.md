# Turbine: Facebook’s Service Management Platform for Stream Processing
:turbine:paper:three-pass:second-pass:

## System Overview
Components:
- Job Management layer: what to run?
- Task Management layer: where to run?
- Resource Management layer: how to run?
Streaming data model, communicate using _Scribe_ (a persistent message bus).

## Job Management
streaming application --(compile & optimize)--> _jobs_
ACIDF configuration changes.
Components:
- Job Store: repository of current and desired configuration parameters.
- Job Service: guarantees atomic job changes to Job Store.
- State Syncer: execute job updates on _jobs_ (state: current -> desired).
Hierarchical Expected Job Configuration:
- Isolated & Consistent
- Base > Provisioner > AutoScaler > OnCall
State Syncer:
- Atomic & Durable & Fault-tolerant (commit; sync cfgs; abort/reschedule)
- sync expected and running job configs every 30s:
    + compare expected and running configs, if they differ
    + generate _Execution plan_
    + carry out plan
- Batch simple synchronization (running = expected)
- Paralelize complex synchronization:
    + stop old task
    + redistribute checkpoints to new tasks
    + start new tasks
    + if failed, retry in next sync or quarantine and alert

## Task Placement and Load Balancing
Goals:
1. Schedule tasks without duplication and no task loss.
2. Fail-over tasks to healthy hosts during host failures.
3. Restart tasks upon crashes.
4. Load balance tasks across the cluster for even CPU, Memory and IO usage.
### Shard Manager - Load Balancing:
- Local task manager periodically computes shard load (CPU, memory, etc.)
- Satisfy Turbine container capacity constraints (tier specific)?.
- Mantain global resource balance across cluster.
- Keep load within +/-10% of average load of containers across tier.
How? (Appendix A)
### Task Manager - Scheduling:
- Fetch all tasks every 60s (Task Service fault-tolerance).
- Task Manager computes MD5 of tasks to determine associated shard ID.
- {Task -> Shard} mapping is cached and updated periodically (60s).
### Shard Manager - Failure Handling:
- bi-directional heartbeat.
- no heartbeat for fail-over interval:
    + shard manager assumes task manager is dead.
    + regenerate mappings for shards in dead container.
    + invoke shard movement protocol (Appendix A).
- how to avoid duplicate shards?
    + time out connection to shard manager before fail-over interval period.
    + Turbine container reboots (and reconnects)?.
    + if it can't reconnect before fail-over it is treated as a new container.

## Elastic Resource Management
Reacts to load changes on (task|job|cluster). Goals:
- ensure sufficient resources.
- maintain efficient cluster-wide resource utilization.
- minimizes unnecessary churn in the system (e.g., restarting tasks).
Reactive Auto Scaler (Appendix C)
Proactive Auto Scaler:
- Resource Estimators: usage of CPU, memory, network bandwidth, and disk I/O
    (Appendix D)
- Plan Generator: construct resource adjustment plan
    (Appendix E)
Preactive Auto Scaler:
- Pattern Analyzer:
    + infer patterns based on seen data
    + prune out potentially destabilizing scaling decisions
    + Resource Adjustment Data (Appendix F)
    + Historical Workload Patterns: diurnal load patterns, 14d historic data
Untriaged Problems:
- Identify misbehavior symptoms
- Fire operator alerts
- Possible reasons:
    + hardware issues
    + bad user updates of the job logic
    + dependency failures
    + system bugs
Vertical vs. Horizontal:
- Vertical: resource allocation changes within the task level
- Horizontal: change number of tasks to increase or decrease job parallelism
- favor vertical scaling until a predefined limit, then scale horizontally.
Capacity Management:
- monitor resource usage of jobs in a cluster
- disaster recovery: prioritize privileged jobs
