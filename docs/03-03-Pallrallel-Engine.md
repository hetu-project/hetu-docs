# [Hetu Parallel Engine](https://github.com/hetu-project/hetu-parallel-engine) 

Hetu Parallel Engine is a high-performance blockchain consensus engine that combines the power of CometBFT with a customized DAG-based Narwhal consensus implementation. 

## Architecture

The engine consists of two main components:

- **hetu-cometbft**: A customized version of CometBFT based on Cosmos-SDK
- **hetu-consensus**: A DAG-based consensus implementation derived from Narwhal

### System Design

```ascii:/README.md
+----------------+        +------------------+
|  hetu-cometbft |  <---> |  hetu-consensus  |
|   (CometBFT)   |        |    (Narwhal)     |
+----------------+        +------------------+

The two components communicate via socket connection, where hetu-consensus (Narwhal) handles the ordering consensus.
```
### Features
1. High-throughput consensus mechanism
2. DAG-based parallel transaction processing
3. Compatible with Cosmos-SDK ecosystem
4. Customized CometBFT implementation
5. Socket-based inter-component communication
6. Optimized for performance and scalability

## Components
### hetu-cometbft
A fork of CometBFT with customizations for:

- Integration with Narwhal consensus
- Enhanced performance optimizations
- Additional functionality for the Hetu ecosystem

### hetu-consensus
A customized implementation of the Narwhal consensus protocol featuring:

- DAG-based transaction ordering
- High-throughput processing
- Optimized network communication
