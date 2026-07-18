# Fluent Bit - Sysmon collector

Ships Sysmon events from the Windows endpoint VM into OpenSearch.

## Deploy

1. Install Fluent Bit 5.0.8 on the endpoint (verify the SHA256 against the vendor's
   published hash before running the installer).
2. Copy `sysmon-to-opensearch.conf` to the endpoint, e.g. `C:\fluent-bit-lab\`.
3. Replace `CHANGEME_OPENSEARCH_ADMIN_PASSWORD` with the real OpenSearch password.
   **Never commit the populated file.**
4. Run:

```
& "C:\Program Files\fluent-bit\bin\fluent-bit.exe" -c "C:\fluent-bit-lab\sysmon-to-opensearch.conf"
```

The `opensearch` output is silent on success - a quiet console means it is shipping.
Errors only appear on failure, and `Trace_Error On` prints the actual OpenSearch response.

## Why each setting

| Setting | Reason |
|---------|--------|
| `Read_Existing_Events false` | Ship only new events, not the whole channel backlog |
| `Remove StringInserts` | Mixed-type array that broke dynamic mapping; it duplicates `Message` content |
| `Suppress_Type_Name On` | Required - OpenSearch rejects the legacy `_type` field Fluent Bit otherwise sends |
| `tls.verify Off` | Lab only. The cluster uses bundled self-signed demo certificates |
| `Index sysmon-logs` | This is an **alias**, not an index. ISM rollover moves it across `sysmon-logs-00000N` transparently, so this value never changes |
| `Trace_Error On` | Prints the real per-item bulk error. Without it, failures show only as "failed to flush chunk" |

## Known gaps (lab vs production)

- **No `DB` setting** - the read position is in-memory only, so events generated while
  Fluent Bit is stopped are never collected. Production should set a `DB` path so the
  bookmark survives restarts.
- **`storage.type` is memory** - unflushed chunks are lost if the process dies.
  Production should use filesystem buffering.
- **Password in plaintext config** - production should inject it from a secrets store.
- **Admin credentials** - production should use a dedicated least-privilege ingest user.
