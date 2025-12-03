# Observability Requirements - Development Instructions for LLM

**Context**: When developing, reviewing, or deploying enterprise LLM services, you MUST implement all observability requirements specified in this document. Observability MUST be implemented from day one, not added later.

**Enforcement**: Observability requirements MUST be validated before production deployment. Without proper observability, services cannot be monitored, debugged, or meet SLAs.

---

## RULE: Metrics Requirements

### MUST IMPLEMENT: System Metrics

**Rule 1.1: Resource Metrics**
- IMPLEMENT: CPU utilization (per container/pod)
- IMPLEMENT: Memory utilization (per container/pod)
- IMPLEMENT: GPU utilization (if applicable)
- IMPLEMENT: Disk I/O and network I/O
- IMPLEMENT: Resource quotas and limits
- REQUIRED: Metrics exposed at `/metrics` endpoint in Prometheus format
- FORBIDDEN: No system metrics exposed

**Rule 1.2: Service Health Metrics**
- IMPLEMENT: Service availability (uptime percentage)
- IMPLEMENT: Health check status
- IMPLEMENT: Service readiness and liveness
- IMPLEMENT: Dependency health (database, cache, etc.)
- REQUIRED: Health metrics updated every 15 seconds (maximum)

**Rule 1.3: Infrastructure Metrics**
- IMPLEMENT: Container/Pod count
- IMPLEMENT: Replica availability
- IMPLEMENT: Node resource availability
- IMPLEMENT: Network latency and bandwidth
- REQUIRED: Infrastructure metrics collected continuously

**Required Configuration**:
```yaml
metrics:
  system:
    cpu_enabled: true             # REQUIRED: true
    memory_enabled: true          # REQUIRED: true
    gpu_enabled: true             # REQUIRED: if GPU used
    collection_interval: 15s      # REQUIRED: ≤ 15 seconds
  export:
    format: prometheus            # REQUIRED: prometheus
    endpoint: /metrics            # REQUIRED: /metrics endpoint
```

### MUST IMPLEMENT: Application Metrics

**Rule 1.4: Request Metrics**
- IMPLEMENT: Request rate (requests per second)
- IMPLEMENT: Request latency (P50, P95, P99)
- IMPLEMENT: Request success rate
- IMPLEMENT: Request error rate by error type
- IMPLEMENT: Request size (input/output tokens, bytes)
- REQUIRED: Latency percentiles (P50, P95, P99) for all requests
- FORBIDDEN: Only average latency (must include percentiles)

**Rule 1.5: LLM-Specific Metrics**
- IMPLEMENT: Time to First Token (TTFT) - P50, P95, P99
- IMPLEMENT: Time per Output Token (TPOT) - P50, P95, P99
- IMPLEMENT: End-to-end latency - P50, P95, P99
- IMPLEMENT: Tokens per second (TPS)
- IMPLEMENT: Token budget utilization
- IMPLEMENT: Cache hit ratio
- REQUIRED: All LLM-specific metrics exposed
- REQUIRED: Streaming metrics for streaming responses

**Rule 1.6: Model Performance Metrics**
- IMPLEMENT: Model inference latency
- IMPLEMENT: Model queue depth
- IMPLEMENT: Model batch size
- IMPLEMENT: GPU utilization per model
- IMPLEMENT: Model memory usage
- REQUIRED: Per-model metrics if multiple models

**Required Configuration**:
```yaml
metrics:
  application:
    request_latency_enabled: true # REQUIRED: true
    llm_metrics_enabled: true    # REQUIRED: true
    percentiles: [50, 95, 99]    # REQUIRED: [50, 95, 99]
  retention: 30d                  # REQUIRED: ≥ 30 days
```

### MUST IMPLEMENT: Business Metrics

**Rule 1.7: Usage Metrics**
- IMPLEMENT: Active users per period (DAU/WAU/MAU)
- IMPLEMENT: Requests per user
- IMPLEMENT: Requests per tenant
- IMPLEMENT: Feature usage statistics
- IMPLEMENT: Geographic distribution of requests
- REQUIRED: Business metrics updated daily (minimum)

**Rule 1.8: Quality Metrics**
- IMPLEMENT: Response quality scores (if applicable)
- IMPLEMENT: User satisfaction metrics
- IMPLEMENT: Error rates by category
- IMPLEMENT: Guardrail trigger rates
- IMPLEMENT: Content moderation statistics
- REQUIRED: Quality metrics tracked continuously

**Rule 1.9: Cost Metrics**
- IMPLEMENT: Cost per request
- IMPLEMENT: Cost per user
- IMPLEMENT: Cost per tenant
- IMPLEMENT: Token usage costs
- IMPLEMENT: Infrastructure costs
- REQUIRED: Cost attribution for all resources

**Metrics Standards**:
- Format: Prometheus-compatible metrics format
- Naming: Follow Prometheus naming conventions (snake_case, units)
- Labels: Use consistent label names (service, environment, tenant)
- Cardinality: Limit label cardinality to prevent metric explosion
- Retention: Minimum 30 days retention for metrics

---

## RULE: Logging Requirements

### MUST IMPLEMENT: Application Logging

**Rule 2.1: Structured Logging**
- IMPLEMENT: JSON format for ALL logs
- IMPLEMENT: Consistent log structure across services
- IMPLEMENT: Standard fields: timestamp, level, service, message, trace_id
- IMPLEMENT: Context fields: user_id, tenant_id, request_id, correlation_id
- FORBIDDEN: Unstructured logs (plain text, multi-line)
- FORBIDDEN: Logs without correlation IDs

**Rule 2.2: Log Levels**
- IMPLEMENT: ERROR - Errors requiring immediate attention
- IMPLEMENT: WARN - Warnings that may require attention
- IMPLEMENT: INFO - Informational messages about normal operation
- IMPLEMENT: DEBUG - Detailed information for debugging (development only)
- REQUIRED: Appropriate log level for each message
- FORBIDDEN: DEBUG logs in production

**Rule 2.3: Sensitive Data Protection**
- IMPLEMENT: NO PII in logs (redacted or masked)
- IMPLEMENT: NO secrets or credentials in logs
- IMPLEMENT: NO full prompts/responses in logs (use hashes or summaries)
- IMPLEMENT: Data classification tags in logs
- FORBIDDEN: PII in logs without redaction
- FORBIDDEN: Secrets or credentials in logs

**Required Configuration**:
```yaml
logging:
  format: json                    # REQUIRED: json
  level: info                     # REQUIRED: info (production) | debug (development)
  structured_fields:
    - timestamp                   # REQUIRED
    - level                       # REQUIRED
    - service                     # REQUIRED
    - message                     # REQUIRED
    - trace_id                    # REQUIRED
    - user_id                     # REQUIRED: if applicable
    - tenant_id                   # REQUIRED: if multi-tenant
    - request_id                  # REQUIRED
  sensitive_data:
    pii_redaction: true           # REQUIRED: true
    secrets_redaction: true       # REQUIRED: true
    prompt_truncation: true       # REQUIRED: true
```

### MUST IMPLEMENT: Request Logging

**Rule 2.4: Request Metadata**
- IMPLEMENT: Request ID (correlation ID) in ALL request logs
- IMPLEMENT: User ID and tenant ID in ALL request logs
- IMPLEMENT: Timestamp and duration for ALL requests
- IMPLEMENT: HTTP method and endpoint
- IMPLEMENT: Request size (bytes, tokens)
- REQUIRED: Correlation ID propagation across services

**Rule 2.5: Request Context**
- IMPLEMENT: User agent and IP address
- IMPLEMENT: Authentication information (user, roles)
- IMPLEMENT: Feature flags and configuration
- IMPLEMENT: Model version and parameters
- REQUIRED: All context captured in logs

**Rule 2.6: Response Metadata**
- IMPLEMENT: Response status code
- IMPLEMENT: Response size (bytes, tokens)
- IMPLEMENT: Processing time breakdown
- IMPLEMENT: Error details (if applicable)
- REQUIRED: Response metadata for all requests

**Log Standards**:
- Format: JSON structured logs
- Encoding: UTF-8
- Timestamp: ISO 8601 format with timezone
- Collection: Centralized log collection (Loki, Elasticsearch, etc.)
- Retention: Minimum 90 days for audit logs, 30 days for application logs

---

## RULE: Tracing Requirements

### MUST IMPLEMENT: Distributed Tracing

**Rule 3.1: Trace Generation**
- IMPLEMENT: Trace EVERY request through the system
- IMPLEMENT: Trace ID propagation across service boundaries
- IMPLEMENT: Span creation for ALL significant operations
- IMPLEMENT: Parent-child span relationships
- FORBIDDEN: Requests without traces
- FORBIDDEN: No trace ID propagation

**Rule 3.2: Span Attributes**
- IMPLEMENT: Service name and version
- IMPLEMENT: Operation name
- IMPLEMENT: Duration and status
- IMPLEMENT: Tags: user_id, tenant_id, request_id, model_name
- IMPLEMENT: Events: key milestones in processing
- REQUIRED: All spans include required attributes

**Rule 3.3: Trace Sampling**
- IMPLEMENT: 100% sampling for errors
- IMPLEMENT: Configurable sampling rate for successful requests (default: 10%)
- IMPLEMENT: Head-based sampling (consistent sampling per request)
- IMPLEMENT: Sampling configuration per environment
- REQUIRED: All errors traced (100% sampling)
- REQUIRED: Consistent sampling per request

**Required Configuration**:
```yaml
tracing:
  enabled: true                   # REQUIRED: true
  provider: opentelemetry        # REQUIRED: opentelemetry
  sampling:
    error_rate: 1.0               # REQUIRED: 1.0 (100% for errors)
    success_rate: 0.1             # REQUIRED: 0.1 (10% default)
    head_based: true              # REQUIRED: true
  export:
    format: otlp                  # REQUIRED: otlp
    endpoint: jaeger:4317         # REQUIRED: configured endpoint
```

### MUST IMPLEMENT: Trace Coverage

**Rule 3.4: Request Processing**
- IMPLEMENT: Trace API gateway entry
- IMPLEMENT: Trace authentication and authorization
- IMPLEMENT: Trace request routing and transformation
- IMPLEMENT: Trace response generation
- REQUIRED: End-to-end trace coverage

**Rule 3.5: LLM Operations**
- IMPLEMENT: Trace model inference calls
- IMPLEMENT: Trace RAG retrieval operations
- IMPLEMENT: Trace vector database queries
- IMPLEMENT: Trace cache operations
- REQUIRED: All LLM operations traced

**Rule 3.6: External Dependencies**
- IMPLEMENT: Trace database queries
- IMPLEMENT: Trace cache operations
- IMPLEMENT: Trace external API calls
- IMPLEMENT: Trace message queue operations
- REQUIRED: All external dependencies traced

**Tracing Standards**:
- Standard: OpenTelemetry
- Format: OTLP (OpenTelemetry Protocol)
- Sampling: Head-based sampling for consistency
- Retention: Minimum 7 days retention for traces
- Visualization: Jaeger, Tempo, or compatible UI

---

## RULE: Alerting Requirements

### MUST IMPLEMENT: Alert Categories

**Rule 4.1: Critical Alerts**
- IMPLEMENT: Service unavailable (downtime) → PagerDuty/On-Call
- IMPLEMENT: SLA violations (latency > threshold) → PagerDuty/On-Call
- IMPLEMENT: Error rate spike (> 5% for 5 minutes) → PagerDuty/On-Call
- IMPLEMENT: Security incidents → PagerDuty/On-Call
- REQUIRED: Critical alerts trigger on-call response
- FORBIDDEN: Critical alerts without immediate notification

**Rule 4.2: Warning Alerts**
- IMPLEMENT: Performance degradation (latency trending up) → Email/Slack
- IMPLEMENT: Resource exhaustion warnings → Email/Slack
- IMPLEMENT: High error rates (but not critical) → Email/Slack
- IMPLEMENT: Capacity warnings → Email/Slack
- REQUIRED: Warning alerts for proactive issue detection

**Rule 4.3: Info Alerts**
- IMPLEMENT: Deployment events → Dashboard only
- IMPLEMENT: Configuration changes → Dashboard only
- IMPLEMENT: Scheduled maintenance → Dashboard only
- IMPLEMENT: Business metric changes → Dashboard only
- REQUIRED: Info alerts logged but not noisy

**Required Configuration**:
```yaml
alerting:
  critical:
    pagerduty_integration: true   # REQUIRED: true
    channels: [pagerduty, slack]  # REQUIRED: configured channels
  warning:
    email: true                   # REQUIRED: true
    channels: [email, slack]      # REQUIRED: configured channels
```

### MUST IMPLEMENT: Alert Rules

**Rule 4.4: Availability Alerts**
- IMPLEMENT: Service uptime < 99.9% → Critical alert
- IMPLEMENT: Health check failures → Critical alert
- IMPLEMENT: Dependency failures → Critical alert
- REQUIRED: Availability alerts configured

**Rule 4.5: Performance Alerts**
- IMPLEMENT: P95 latency > SLA threshold → Warning alert
- IMPLEMENT: P99 latency > SLA threshold → Critical alert
- IMPLEMENT: TTFT > 500ms (P95) → Warning alert
- IMPLEMENT: Queue depth > threshold → Warning alert
- REQUIRED: Performance alerts aligned with SLAs

**Rule 4.6: Error Alerts**
- IMPLEMENT: Error rate > 1% (5-minute window) → Warning alert
- IMPLEMENT: Error rate > 5% (5-minute window) → Critical alert
- IMPLEMENT: 5xx errors > threshold → Critical alert
- IMPLEMENT: Model inference failures → Critical alert
- REQUIRED: Error alerts configured with thresholds

**Rule 4.7: Resource Alerts**
- IMPLEMENT: CPU utilization > 80% → Warning alert
- IMPLEMENT: Memory utilization > 80% → Warning alert
- IMPLEMENT: GPU utilization > 90% → Warning alert
- IMPLEMENT: Disk usage > 85% → Warning alert
- REQUIRED: Resource alerts before exhaustion

**Rule 4.8: Security Alerts**
- IMPLEMENT: Failed authentication spike → Critical alert
- IMPLEMENT: Rate limit violations spike → Warning alert
- IMPLEMENT: Unusual access patterns → Critical alert
- IMPLEMENT: Guardrail trigger spike → Warning alert
- REQUIRED: Security alerts for all security events

**Required Configuration**:
```yaml
alerting:
  rules:
    - name: high_error_rate
      condition: error_rate > 0.01  # 1%
      duration: 5m
      severity: critical
    - name: high_latency
      condition: p95_latency > 2000ms
      duration: 5m
      severity: warning
    - name: service_down
      condition: uptime < 0.999  # 99.9%
      duration: 1m
      severity: critical
```

---

## RULE: Performance Monitoring

### MUST IMPLEMENT: SLA Monitoring

**Rule 5.1: Latency SLAs**
- IMPLEMENT: P50, P95, P99 latency tracking
- IMPLEMENT: SLA violation detection
- IMPLEMENT: Latency trend analysis
- IMPLEMENT: Comparison to SLA targets
- REQUIRED: Continuous SLA monitoring

**Rule 5.2: Availability SLAs**
- IMPLEMENT: Uptime percentage tracking
- IMPLEMENT: Downtime tracking
- IMPLEMENT: SLA violation detection
- IMPLEMENT: MTBF and MTTR tracking
- REQUIRED: Real-time availability monitoring

**Rule 5.3: Throughput SLAs**
- IMPLEMENT: Requests per second tracking
- IMPLEMENT: Tokens per second tracking
- IMPLEMENT: Concurrent users tracking
- IMPLEMENT: Capacity utilization tracking
- REQUIRED: Throughput metrics for capacity planning

### MUST IMPLEMENT: Performance Dashboards

**Rule 5.4: Service Overview Dashboard**
- IMPLEMENT: Current request rate
- IMPLEMENT: Current latency (P50, P95, P99)
- IMPLEMENT: Current error rate
- IMPLEMENT: Service health status
- REQUIRED: Real-time service overview

**Rule 5.5: Performance Dashboard**
- IMPLEMENT: Latency trends (1h, 24h, 7d)
- IMPLEMENT: Throughput trends
- IMPLEMENT: Resource utilization
- IMPLEMENT: Queue depth and processing time
- REQUIRED: Historical performance trends

**Rule 5.6: User Experience Dashboard**
- IMPLEMENT: Time to First Token trends
- IMPLEMENT: End-to-end latency
- IMPLEMENT: User satisfaction metrics
- IMPLEMENT: Feature usage statistics
- REQUIRED: User-focused metrics

**Required Configuration**:
```yaml
performance_monitoring:
  sla:
    latency:
      p95_target: 2000ms          # REQUIRED: defined target
      p99_target: 5000ms          # REQUIRED: defined target
    availability:
      target: 0.999               # REQUIRED: 99.9% target
  dashboards:
    service_overview: true        # REQUIRED: true
    performance: true             # REQUIRED: true
    user_experience: true         # REQUIRED: true
```

---

## VALIDATION CHECKLIST

Before production deployment, verify ALL requirements:

```
METRICS:
[ ] System metrics exposed (CPU, memory, GPU, disk, network)
[ ] Application metrics exposed (request rate, latency, errors)
[ ] LLM-specific metrics exposed (TTFT, TPOT, tokens/sec)
[ ] Business metrics exposed (usage, quality, cost)
[ ] Metrics in Prometheus format at /metrics endpoint
[ ] Metrics retention configured (minimum 30 days)

LOGGING:
[ ] Structured JSON logging implemented
[ ] Standard log fields (timestamp, level, service, trace_id)
[ ] Request logging with correlation IDs
[ ] Security event logging (auth, authorization, security)
[ ] PII redaction in logs
[ ] Centralized log collection configured
[ ] Log retention configured (minimum 90 days for audit logs)

TRACING:
[ ] Distributed tracing implemented (OpenTelemetry)
[ ] Trace ID propagation across services
[ ] Span creation for key operations
[ ] Trace sampling configured (100% errors, 10% success)
[ ] Trace export to centralized system
[ ] Trace visualization available

ALERTING:
[ ] Critical alerts configured (PagerDuty/On-Call)
[ ] Warning alerts configured (Email/Slack)
[ ] Availability alerts configured
[ ] Performance alerts configured (aligned with SLAs)
[ ] Error rate alerts configured
[ ] Resource alerts configured
[ ] Security alerts configured

DASHBOARDS:
[ ] Service overview dashboard created
[ ] Performance dashboard created
[ ] User experience dashboard created
[ ] Business metrics dashboard created
[ ] Cost tracking dashboard created
[ ] Dashboards accessible to relevant teams

DOCUMENTATION:
[ ] Observability architecture documented
[ ] Metrics documented with descriptions
[ ] Alert runbooks created
[ ] Dashboard documentation created
[ ] On-call procedures documented
```

---

## ENFORCEMENT

- **Day One**: Observability MUST be implemented from day one
- **Pre-deployment**: All observability requirements MUST be validated
- **Continuous**: Observability MUST be continuously monitored
- **Failure to meet requirements = Deployment blocked**
