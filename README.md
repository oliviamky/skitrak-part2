# SkiTrak — Distributed Ski Lift Event Processing (Part 2)

SkiTrak is a Java-based distributed systems project that ingests simulated ski-lift ride events and delivers live leaderboard updates. **Part 2 builds on the HTTP ingestion and WebSocket delivery implemented in Part 1**, with a focus on decoupling event intake from downstream processing through asynchronous messaging.

> **Project status:** Part 1 code is the starting point for this repository. Part 2 components are under development; the architecture and performance goals below should not be interpreted as completed or benchmarked features.

## Goals

- Accept ski-lift events through an HTTP API.
- Introduce a queue between event ingestion and event processing.
- Process events with consumer workers independently of incoming HTTP requests.
- Explore throughput, queue depth, consumer lag, failure handling, and event loss under load.
- Maintain real-time leaderboard delivery through WebSocket connections as the system evolves.

## Architecture

### Part 1 baseline

```text
Multithreaded Load Generator
          |
          | HTTP POST 
          v
Java Servlet (Tomcat)
          |
          v
In-memory event handling / leaderboard
          |
          v
WebSocket /leaderboard/{resortID}
          |
          v
Leaderboard Subscribers
```

### Part 2 target design (work in progress)

```text
Multithreaded Load Generator
          |
          | HTTP POST
          v
HTTP Ingestion Service
          |
          v
      Message Queue
          |
          v
    Consumer Worker(s)
          |
          v
  Event Processing / State
          |
          v
  Leaderboard Updates
          |
          v
 WebSocket Subscribers
```

The queue and consumer implementation, deployment configuration, and failure-handling behavior will be documented once implemented and tested.

## Technology Stack

- **Language:** Java
- **Web server:** Apache Tomcat 9 / Java Servlets
- **Real-time delivery:** WebSocket
- **Load testing:** Multithreaded Java client
- **Deployment (Part 1 baseline):** AWS EC2
- **Build:** Maven (if using the original Part 1 Maven project)
- **Messaging:** To be specified after Part 2 implementation

## Event Workload

The Part 1 load generator simulates ski-lift rides with fields such as resort ID, skier ID, lift ID, season, day, and time. The baseline workload contains **500,000 generated events** across a warm-up phase and a main test phase.

## Performance Evaluation

Part 2 will be evaluated using the following measurements:

| Metric | What it measures | Working target |
| --- | --- | --- |
| HTTP throughput | Requests accepted per second | Measure under different client concurrency levels |
| HTTP latency | Mean, p50, p95, and p99 response times | Report observed values |
| Queue depth | Events waiting to be consumed | Below 1,000 during steady-state testing |
| Consumer lag | Delay between event arrival and processing | Below 100 ms |
| Event loss | Accepted events not successfully processed | Zero |
| WebSocket delivery | Update latency and connection stability | Measure and report |

## Repository Organization

This repository starts from the Part 1 implementation. Refer to the actual source tree for module paths. This section will be updated when the Part 2 producer, queue, and consumer modules are finalized.

## Running the Project

Setup instructions are being updated for Part 2. The existing Part 1 components use a Java/Tomcat environment. Before running the full distributed pipeline, configure the chosen messaging system and consumer processes, then document startup commands and required environment variables here.

