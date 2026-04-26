# GCP Tooling

## Log Queries (Cloud Logging / gcloud)

### Application errors (stdout logs with error details)
```bash
gcloud logging read '
  resource.type="cloud_run_revision"
  resource.labels.service_name="<SERVICE_NAME>"
  severity>=ERROR
  timestamp>="<START_UTC>"
  timestamp<="<END_UTC>"
' --limit=30 --format=json --project=<GCP_PROJECT>
```

### HTTP request logs (status codes, latencies)
```bash
gcloud logging read '
  resource.type="cloud_run_revision"
  resource.labels.service_name="<SERVICE_NAME>"
  httpRequest.status>=500
  timestamp>="<START_UTC>"
  timestamp<="<END_UTC>"
' --limit=30 --format=json --project=<GCP_PROJECT>
```

### Application-level logs (exclude request logs)
```bash
gcloud logging read '
  resource.type="cloud_run_revision"
  resource.labels.service_name="<SERVICE_NAME>"
  NOT logName:"requests"
  severity>=ERROR
  timestamp>="<START_UTC>"
  timestamp<="<END_UTC>"
' --limit=30 --format=json --project=<GCP_PROJECT>
```

### Filter by endpoint
Add `"<endpoint_path>"` to any query above.

## Time window

Use the alert start time minus 5 minutes to start time plus 10 minutes. Convert local timezone to UTC.

## Broadening queries

If the first query returns only HTTP request logs without error details:
- Remove the endpoint filter
- Try `NOT logName:"requests"` to get application stdout/stderr
- Lower severity to `>=WARNING`

## Tips

- **`user-agent: APIs-Google`** = Pub/Sub pushing to a worker
- **`from: noreply@google.com`** = Cloud Scheduler or Pub/Sub trigger
- **Small response body with 500** = generic error body, check application logs
- **Latency < 20ms with 500** = service rejecting immediately, not a timeout — check for startup/config/DB issues
- **Same instanceId across errors** = single container affected. Different instanceIds = systemic

## Service Reference

Populate this table with your project's service names, repos, and runtimes:

| Service | Repo | Runtime | Notes |
|---------|------|---------|-------|
| `<service-name>` | `<repo>` | `<runtime>` | `<description>` |

GCP Project: `<GCP_PROJECT>`
Region: `<REGION>`
