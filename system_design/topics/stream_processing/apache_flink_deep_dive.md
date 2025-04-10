# SD: Exactly-Once Processing (Apache Flink)

## Apache Flink (2011) 
1) Flink is a unified `batch and stream processing system` 
- batching and streaming share common abstractions
- a `stream` is a potentially unbounded source of records
- a `batch` is just a stream bounded in size

2) Flink allows for stateful operators (incoming messages build up state)
- operators may be chained together by intermediate streams
- state is updated by each input **"exactly once"** 
- state for operators is maintained in fault-tolerant manner 

## Why Stream processing 
- generally data is produced at arbitrary moments
- its also faster to get insights if not in some interval (batch) 
- cuts down on feed back loop of returning data 
- lambdas use `streams` for `fast approximations` and `batches` for `slow exact computations`  (**flink does both!**) 

## Streams in Flink: 
- in flink, a stream is a potentially unlounded source of records (batches are finite streams) 
- flink jobs can be represented as a `DAG (directed acyclical graph)` of operators & streams

  <img src="../../img/apache-flink-1.png" alt="apache-flink-1" width="400"/>


## Architecture Overview:
- client code is processed into DAG, job manager schedules operators/task managers (these have access to message broker + storage) t
- the job manager triggers checkpoints and schedules heartbeats with task managers 
- the job manager **check points its own state to the service coordinator for fault-tolerance /persistence** 
- the service coordinator can spin-up and back up job manager if heartbeas to the job manager fail

## Types of Streams: 
1) `Pipelined` - two consecutive map functions on an incoming record 
- leads to back pressure between operators
- use optional configurable buffer pools to mitigate back pressure 
	- size threshold -> based on buffer size (larger = more throughput)
	- time threshold -> based on time (smaller = lower latency) 

2) `Blocking` (for bounded streams aka batches) - all data is materialized to disk

## Flink Event Ordering:

