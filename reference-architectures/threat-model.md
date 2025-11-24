# LLM Services Threat Model

A comprehensive threat model for on-premise LLM infrastructure covering API endpoints, web UI, RAG systems, vector databases, and related components.

## Scope

This threat model covers:

- OpenWebUI and administrative interfaces
- API gateways, service mesh, and request brokers
- Inference runtimes (vLLM, TensorRT-LLM) and sidecars
- Vector databases (Milvus, FAISS/HNSW) and caches (Redis/Dragonfly)
- Model registries and deployment pipelines (ArgoCD, OCI, HuggingFace mirror)
- Observability systems (Prometheus, OpenTelemetry, ClickHouse logs)

## Assets

### Data Assets
- User prompts and responses, including PII and trade secrets
- Embeddings and documents in vector databases with classification
- Service account secrets, SPIFFE identifiers, tokens
- Model manifests, SBOM, and container artifacts
- Audit logs and SLO metrics
- Policy configurations (guardrails, rate limits, mesh) and ACLs in Milvus

### Infrastructure Assets
- GPU clusters and compute resources
- Network infrastructure and service mesh
- Storage systems (databases, object storage)
- Identity and access management systems

## Threat Actors

| Actor Type | Motivation | Examples |
|------------|-----------|----------|
| External Attacker | Access to corporate data, extortion, sabotage | APT groups, professional pentesters |
| Insider | Data theft, policy bypass, sabotage | Employees, administrators, contractors |
| Compromised Service | Attack escalation after RCE/credential leak | Exploited UI/sidecars, vulnerable libraries |

## Threat Categories

### 4.1 Availability

| Target | Scenario | Vector | Mitigation | Detection |
|--------|----------|--------|------------|-----------|
| API Gateway/Mesh | DoS via massive requests or token exhaustion | Mass requests, token storms, client bugs | Rate limiting per tenant, circuit breakers, isolated queues | RPS metrics, queue depth, burn-rate alerts |
| Vector DBs | Overload via heavy Search/Query operations | Large topK queries, multiple filters | TopK limits, timeouts, token budgets, read replication | Milvus latency anomalies, shard failure signals |
| Inference Pools | GPU pool isolation by single tenant | Prompts creating long sessions | Preemptive time limits, separate queues, prioritization | GPU load metrics, SLA violation signals |
| OpenWebUI | UI disruption via mass logins | Credential stuffing, bot scripts | WAF, MFA, CAPTCHA, IP allow-lists | Login logs, UEBA on atypical IPs |

### 4.2 Confidentiality

| Asset | Scenario | Vector | Protection Measures | Detection |
|-------|----------|--------|-------------------|-----------|
| Prompts/Responses | Leakage via compromised UI or logging | XSS, insecure plugins, incorrect log ACLs | Content redaction, log encryption, storage separation | Log access monitoring, DLP signatures |
| Vector Collections | Unauthorized collection reading or embedding exfiltration | Role escalation, direct Milvus connection | Row-level security, mTLS with client certs, Vault-issued tokens | Alerts on Search outside assigned tags, bulk export analysis |
| Models & Configs | Artifact theft | ArgoCD/registry compromise | Signed manifests, immutability, network policies | SIEM on image downloads outside CI/CD |
| Secrets | Vault key or SPIFFE token acquisition | Sidecar leak, pod substitution | Bound service accounts, secret zeroization, PoLP | Secret request monitoring, Vault alerts |

### 4.3 Integrity

| Component | Scenario | Measures | Detection |
|-----------|----------|----------|-----------|
| Guardrails/Policies | Filtering/classification rule substitution | GitOps + signatures, approval workflow, immutable config maps | Drift detection Git ↔ cluster, ConfigMap change alerts |
| Vector Data | False document/embedding injection | Generator signatures, source validation, quarantine pools | Alerts on inserts from atypical services, checksum verification |
| Models | Unapproved version deployment | Release pipeline checks, registry RBAC | Alerts on model hash mismatch, registry vs Argo record comparison |
| Logs | Audit log manipulation | Append-only storage, WORM buckets | Checksums, alerts on retroactive changes |

### 4.4 Access Control

| Target | Scenario | Measures |
|--------|----------|----------|
| OpenWebUI Roles | Self-addition to tenant group | Just-in-time access, approval flow, regular access reviews |
| API Keys | Token reuse in different tenant | Audience binding, short TTL, device binding |
| Milvus ACL | Privilege escalation via inherited roles | Diff policies, separation of duties (ops ≠ security) |

## Attack Scenarios

### 1. Prompt Injection → Data Exfiltration
**Scenario**: Attacker sends prompts with instructions to bypass guardrails and output confidential documents.

**Mitigation**:
- Multi-layer filters (LLM + rules)
- Contextual tag validation
- Output only from allowed collections
- Rate limiting on suspicious patterns

**Detection**: Anomalous prompt patterns, guardrail bypass attempts, unusual data access

### 2. Compromised OpenWebUI Plugin
**Scenario**: User installs unofficial plugin (JS) that intercepts prompts.

**Mitigation**:
- Plugin allow-list
- Content Security Policy (CSP)
- Plugin signature verification
- Sandboxed iframe execution

**Detection**: Unauthorized plugin installation, XSS attempts, unusual network traffic

### 3. Vector DB Scraping via Service Token
**Scenario**: Attacker extracts service account token from container and bulk downloads embeddings.

**Mitigation**:
- Secret rotation
- Maximum request limits
- Honey collections for detection
- Token binding to specific services
- Network policies restricting access

**Detection**: Unusual bulk queries, token usage anomalies, access to honey collections

### 4. Model Supply Chain Attack
**Scenario**: Malicious model introduced via registry.

**Mitigation**:
- SBOM analysis
- Antivirus scanning
- Quarantined namespace testing
- Model signature verification
- Approval workflow

**Detection**: Unsigned model deployments, SBOM anomalies, registry access alerts

### 5. Cross-Tenant Caching Leak
**Scenario**: User attempts to retrieve other users' responses from Redis.

**Mitigation**:
- Namespaced keys (`tenant:session:id`)
- Payload encryption
- Redis ACL per namespace
- Cache key validation

**Detection**: Cross-tenant cache access attempts, key pattern anomalies

### 6. Data Poisoning via Public Sources
**Scenario**: Attacker introduces modified documents into public knowledge source.

**Mitigation**:
- Digital source signatures
- Checksum verification
- Moderation before embedding generation
- Source reputation tracking

**Detection**: Document integrity failures, source reputation changes

## Detection and Monitoring

### Logging and Telemetry
- OpenWebUI and API gateway logs in ClickHouse with UEBA profiles
- OpenTelemetry traces for key chains (gateway → broker → RAG → model → Milvus) with tenant correlation
- SIEM rules for:
  - Anomalous topK or filter growth in Milvus
  - Frequent authorization errors in OpenWebUI
  - Token quota exceedances per tenant
  - Access attempts to honeypot collections

### Metrics
- Guardrail block rate
- Milvus latency
- Fallback route ratio
- Error rates by tenant and endpoint
- GPU utilization anomalies

### Security Information and Event Management (SIEM)
- Real-time correlation of security events
- User and Entity Behavior Analytics (UEBA)
- Anomaly detection on access patterns
- Automated threat intelligence integration

## Incident Response Plan

### 1. Identification
- Automatic incident creation on critical alerts (SIEM/Prometheus)
- Rapid correlation with tenant and user
- Timeline reconstruction from logs and traces

### 2. Containment
- Revoke OIDC sessions
- Disable affected namespaces (network policies)
- Switch models to read-only mode
- Isolate compromised services

### 3. Eradication
- Rotate secrets/certificates
- Re-index collections if compromised
- Redeploy guardrails or models
- Patch vulnerabilities

### 4. Recovery
- Verify integrity of Milvus replicas and artifacts
- Test SLA compliance
- Gradual service restoration
- Monitor for recurrence

### 5. Post-Incident
- Post-mortem documentation
- Update threat model
- Update runbooks
- Security training if needed

## Additional Security Measures

### Proactive Security
- **Honeypots**: Artificial collections and prompts to detect abuse
- **Penetration Testing**: Regular pentests focusing on UI/API, jailbreak/prompt injection
- **Table-top Exercises**: Regular exercises with security teams and data owners
- **Policy Validation**: Automated policy checks on every release (OPA/Gatekeeper)

### Compliance
- **Data Protection**: GDPR, CCPA compliance measures
- **Audit Requirements**: Comprehensive audit trails
- **Data Residency**: Control over data location
- **Right to Deletion**: Automated data deletion capabilities

## Security Controls Mapping

| Control Category | Technologies | Coverage |
|-----------------|--------------|----------|
| Network Security | Istio, Cilium, Envoy | mTLS, network policies, segmentation |
| Access Control | Keycloak, OPA, SPIRE | Authentication, authorization, service identity |
| Data Protection | Vault, pgcrypto, LUKS | Encryption at rest/transit, secret management |
| Monitoring | Falco, KubeArmor, Prometheus | Runtime security, anomaly detection |
| Content Security | NeMo Guardrails, ModSecurity | Prompt injection protection, WAF |
| Audit | ClickHouse, pgaudit | Comprehensive audit logging |

## Security Control Mapping

### Control Implementation by Threat Category

**Availability Threats**:
- **Control**: Rate limiting, circuit breakers, resource quotas
- **Tools**: Envoy Gateway, Istio, KEDA
- **Monitoring**: Prometheus, Grafana
- **Alerting**: AlertManager, PagerDuty

**Confidentiality Threats**:
- **Control**: Encryption, access control, DLP, data redaction
- **Tools**: Vault, OPA, Immuta, Curie Proxy
- **Monitoring**: SIEM, DLP tools
- **Alerting**: Real-time security alerts

**Integrity Threats**:
- **Control**: Model signing, SBOM, GitOps, audit logs
- **Tools**: Cosign, Syft, ArgoCD, Harbor
- **Monitoring**: GitOps drift detection, SBOM scanning
- **Alerting**: Configuration change alerts

**Access Control Threats**:
- **Control**: RBAC, ABAC, JIT access, approval workflows
- **Tools**: Keycloak, OPA, Ory Keto
- **Monitoring**: Access review reports, UEBA
- **Alerting**: Privilege escalation alerts

## Risk-Based Security Controls

### High-Risk Scenarios

**Prompt Injection → Data Exfiltration**:
- **Risk Level**: Critical
- **Controls Required**:
  - Multi-layer input validation
  - Guardrails (NeMo Guardrails)
  - Context validation
  - Output filtering
  - Rate limiting on suspicious patterns
- **Detection**: Anomaly detection, guardrail bypass alerts
- **Response**: Immediate containment, investigation

**Compromised Service Account**:
- **Risk Level**: Critical
- **Controls Required**:
  - Service account rotation
  - Bound service accounts
  - Secret zeroization
  - Network policies
  - Audit logging
- **Detection**: Unusual service account activity
- **Response**: Immediate token revocation, service isolation

### Medium-Risk Scenarios

**Vector DB Scraping**:
- **Risk Level**: High
- **Controls Required**:
  - Row-level security
  - Access control
  - Rate limiting
  - Audit logging
- **Detection**: Bulk query alerts, access pattern anomalies
- **Response**: Access revocation, investigation

**Model Supply Chain Attack**:
- **Risk Level**: High
- **Controls Required**:
  - Model signing
  - SBOM analysis
  - Quarantine testing
  - Approval workflows
- **Detection**: Unsigned model alerts, SBOM anomalies
- **Response**: Model quarantine, security review

## Security Metrics and KPIs

### Threat Detection Metrics
- Mean Time to Detect (MTTD): < 15 minutes
- False Positive Rate: < 5%
- Detection Coverage: > 95%
- Threat Intelligence Match Rate: > 80%

### Incident Response Metrics
- Mean Time to Contain (MTTC): < 1 hour
- Mean Time to Eradicate (MTTE): < 4 hours
- Mean Time to Recover (MTTR): < 24 hours
- Incident Resolution Rate: > 95%

### Security Control Effectiveness
- Access Control Violations: < 1% of requests
- Encryption Coverage: 100%
- Audit Log Coverage: 100%
- Security Control Compliance: > 98%

## References

- OWASP Top 10 for LLM Applications
- NIST Cybersecurity Framework 2.0
- MITRE ATT&CK for Enterprise
- Cloud Security Alliance (CSA) AI Security Guidelines
- NIST AI Risk Management Framework
- [Cybersecurity Framework](../cybersecurity-framework.md)

