# Turbine: Facebook’s Service Management Platform for Stream Processing
:turbine:paper:three-pass:first-pass:

## What is it?
- fast and scalable task scheduler.
- efficient predictive auto scaler.
- ACID, fault-tolerant (ACIDF) application update mechanism.
- service management platform on top of cluster management systems [^1].
- currently integrates with Tupperware [^2] (low level host management).
- handles high level job and task management to ensure minimum downtime.

## Use cases
- low latency analysis of site content interaction,
- search effectiveness signals,
- recommendation-related activities,
- stream processing in real-time (**assumption**: requires auto scaling).

## Context
- real-time applications.
- cluster management systems [^1].

## Requirements (why?)
- strict Service Level Objectives (SLOs) with low downtime and processing lag,
- respond to failures and load variability.
- facebook scale.

## Contributions
bridge the (**assumption**: there is a...) gap between the capabilities of
existing general-purpose cluster management systems [^1] and Facebook stream
processing requirements.
- An efficient predictive auto scaler that adjusts resource allocation in
  multiple dimensions;
- A scheduling and lifecycle management framework featuring fast task
  scheduling and failure recovery;
- An ACIDF application update mechanism;
- An architecture that decouples what to run (Job Management), where to run
  (Task Management), and how to run (Resource Management) to minimize the
  impact of individual component failures.

### References

[^1]: Aurora, Mesos, Borg, Tupperware, Kubernetes, etc.
[^2]: A. Narayanan. Tupperware: Containerized deployment at Facebook. In
    DockerCon, 2014.
