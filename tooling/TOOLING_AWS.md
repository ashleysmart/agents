# AWS Tooling

## Log Queries (CloudWatch Logs / aws cli)

### Application errors
```bash
aws logs filter-log-events \
  --log-group-name "<LOG_GROUP>" \
  --start-time <START_EPOCH_MS> \
  --end-time <END_EPOCH_MS> \
  --filter-pattern "ERROR" \
  --limit 30 \
  --output json
```

### HTTP 5xx errors
```bash
aws logs filter-log-events \
  --log-group-name "<LOG_GROUP>" \
  --start-time <START_EPOCH_MS> \
  --end-time <END_EPOCH_MS> \
  --filter-pattern '{ $.statusCode >= 500 }' \
  --limit 30 \
  --output json
```

### Application-level logs (exclude health checks)
```bash
aws logs filter-log-events \
  --log-group-name "<LOG_GROUP>" \
  --start-time <START_EPOCH_MS> \
  --end-time <END_EPOCH_MS> \
  --filter-pattern '{ $.level = "ERROR" && $.path != "/health" }' \
  --limit 30 \
  --output json
```

### Filter by endpoint
Add `{ $.path = "<endpoint_path>" }` to the filter pattern.

## Time window

Use the alert start time minus 5 minutes to start time plus 10 minutes. Timestamps are epoch milliseconds.

```bash
# Convert ISO to epoch ms
date -d "2026-04-23T04:40:00Z" +%s000
```

## Broadening queries

If the first query returns only ALB/API Gateway access logs without error details:
- Check the application log group (not the access log group)
- Lower the filter to `WARNING` or remove the severity filter
- Check multiple log streams if using ECS/Lambda

## Tips

- **Lambda timeout** = check CloudWatch for `Task timed out after X seconds`
- **ECS OOM** = check for `OutOfMemoryError` or container exit code 137
- **Same taskId across errors** = single container affected. Different taskIds = systemic
- **`user-agent: Amazon CloudFront`** = CDN origin fetch
- **`user-agent: Elastic-Heartbeat`** = health check, not real traffic

## Service Reference

Populate this table with your project's service names, log groups, and runtimes:

| Service | Log Group | Runtime | Notes |
|---------|-----------|---------|-------|
| `<service-name>` | `<log-group>` | `<runtime>` | `<description>` |

AWS Account: `<ACCOUNT_ID>`
Region: `<REGION>`
