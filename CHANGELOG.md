# Changelog

All notable changes to this project will be documented here.

This project follows [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [0.1.0] - 2025-06-01
### Added
- Initial project scaffold: `node/`, `coordinator/`, `aggregator/`, `protos/`, `scripts/`, `tests/`
- Protobuf definitions: `Header`, `ModelReference`, `GradientSubmission`, `GradientReceipt`, `NodeHeartbeat`, `WorkAssignment`, `StepManifest`
- Node:
  - Heartbeat and training loop
  - Signed gradient submission
  - Model polling via manifest
- Coordinator:
  - Gradient verification and storage to Minio
  - Heartbeat tracking via Redis TTLs
  - Gradient receipt publishing to Redis Stream
  - Manifest creation after aggregation
- Aggregator:
  - FedAvg aggregation of submitted gradients
  - Updated model push to Minio
  - ModelReference publishing to Redis
- Docker Compose config for Redis, Minio, and stub services
- Script to create and upload initial model checkpoint

---

## [0.1.1] - UNRELEASED
### Planned
- Full end-to-end integration test with 2 shards
- Improved error handling for signature verification
- Automatic step ID tracking for manifests
