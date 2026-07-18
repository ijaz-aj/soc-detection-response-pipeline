# Progress Log

## Phase 1 - Detections to live alerts (in progress)
- [x] Host pre-flight: WSL2 memory cap + vm.max_map_count=262144
- [x] Repo scaffold
- [x] OpenSearch + Dashboards stood up
- [x] Raw log ingestion working (Fluent Bit winevtlog -> OpenSearch, Sysmon channel)
- [x] Ingest pipeline `sysmon-parse` (gsub + kv + remove) parsing Message into `sysmon.*` fields
- [x] Index template `sysmon-template` (replicas 0, default_pipeline, keyword ignore_above 8191)
- [x] Reserved TCP 9200/5601 persistently (Windows dynamic port range collisions)
- [x] ISM policy `sysmon-lifecycle` + rollover alias (`sysmon-logs` -> `sysmon-logs-000001`)
- [x] Fluent Bit config committed to `ingest/fluent-bit/` (password placeholder)
- [ ] Elastic decommissioned
- [ ] Sigma rules -> Security Analytics detectors firing
