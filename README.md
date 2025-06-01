# GPU Swap Meet

A minimal, experimental framework for decentralized collaborative training of machine learning models, using spare VRAM across multiple nodes.

## Overview

**GPU Swap Meet** is an early-stage prototype of a distributed gradient submission and aggregation system. It allows independently operating nodes to train small ML models on local data shards, submit signed gradients, and participate in a federated training loop coordinated through a central coordinator and aggregator service.

This is not a privacy-preserving or incentive-driven system (yet). The primary goal of this iteration is to demonstrate the complete data and coordination flow across the system:

- Model broadcast and retrieval
- Gradient computation, signing, compression, and submission
- Gradient verification and storage
- Gradient aggregation and model update
- End-to-end loop closure from nodes pulling the updated model

## Architecture

The system consists of three core services:

### 1. Node (`node/`, Python)
- Downloads the model and trains it on a local shard.
- Signs raw gradient payloads and compresses them.
- Submits gradients and heartbeats to the coordinator.
- Polls for model updates via manifest files in object storage.

### 2. Coordinator (`coordinator/`, Go)
- Verifies gradient signatures using Ed25519.
- Stores compressed gradient blobs in Minio.
- Publishes gradient receipts to a Redis Stream.
- Monitors node heartbeats via TTLs in Redis.
- Publishes updated model manifests after aggregation.

### 3. Aggregator (`aggregator/`, Python)
- Consumes verified gradient receipts from the Redis Stream.
- Downloads and decompresses gradient blobs.
- Aggregates gradients (FedAvg).
- Updates the model and publishes a new signed model reference.

## Data Flow Summary

```text
Node -> Coordinator: Submit compressed, signed gradients
Coordinator -> Minio: Store gradient blob
Coordinator -> Redis: Push gradient receipt
Aggregator -> Redis: Consume receipts
Aggregator -> Minio: Upload new model
Coordinator -> Minio: Upload new manifest
Node -> Minio: Poll and download new model
