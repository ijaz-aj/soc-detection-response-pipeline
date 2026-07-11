# OpenSearch Platform

Single-node OpenSearch 3.7.0 + OpenSearch Dashboards - the SIEM core of this lab.

## Prerequisites
- Docker Desktop (WSL2 backend)
- Host kernel: vm.max_map_count=262144 (via %USERPROFILE%\.wslconfig ->
  kernelCommandLine=sysctl.vm.max_map_count=262144)
- A local .env in this folder (gitignored) with:
    OPENSEARCH_INITIAL_ADMIN_PASSWORD=<your-strong-password>
  (copy ../../.env.example as a starting point)

## Bring up / verify
    docker compose up -d
    docker compose ps
    curl.exe -k -u admin "https://localhost:9200/_cluster/health?pretty"   # expect green/yellow
    # Dashboards: http://localhost:5601  (admin / your password)

## Manual lifecycle
    docker compose stop     # frees RAM, keeps data
    docker compose start
    docker compose down     # removes containers; named volume 'opensearch-data' persists
Note: no restart policy, so the stack does NOT auto-start after reboot
(intentional - manual lab lifecycle). Bring it back with 'docker compose up -d'.

## Lab simplifications (vs production)
- Security plugin ON with bundled demo certs (self-signed) -> curl needs -k
- Heap capped at 2 GB to coexist with other lab tools; prod would use ~50% RAM
- Ports bound to 0.0.0.0 for lab convenience
