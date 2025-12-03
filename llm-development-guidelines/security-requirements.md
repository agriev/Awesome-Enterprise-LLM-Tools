# Security Requirements - Development Instructions for LLM

**Context**: When developing, reviewing, or deploying enterprise LLM services, you MUST implement all security requirements specified in this document. These are mandatory, non-negotiable requirements.

**Enforcement**: Security requirements MUST be validated before production deployment. Security team review is required.

---

## RULE: Authentication & Authorization

### MUST IMPLEMENT: Identity Management

**Rule 1.1: SSO Integration**
- IMPLEMENT: Integration with enterprise identity provider (Keycloak, Okta, Azure AD)
- IMPLEMENT: Support for SAML 2.0 OR OAuth 2.0 / OpenID Connect
- IMPLEMENT: Token-based authentication with token lifetime ≤ 15 minutes
- IMPLEMENT: Automatic token refresh mechanism
- FORBIDDEN: Long-lived tokens (> 15 minutes)
- FORBIDDEN: No authentication or basic auth only

**Rule 1.2: Multi-Factor Authentication**
- IMPLEMENT: MFA REQUIRED for all user access (no exceptions)
- IMPLEMENT: Support for TOTP, SMS, or hardware tokens
- IMPLEMENT: MFA enforcement at identity provider level
- FORBIDDEN: MFA bypass for any user, including admins
- FORBIDDEN: Service accounts using user credentials

**Rule 1.3: Service Account Authentication**
- IMPLEMENT: Service-to-service authentication using SPIFFE/SPIRE OR mTLS
- IMPLEMENT: Short-lived certificates or tokens (< 1 hour)
- IMPLEMENT: Service account credentials stored in secrets management (Vault)
- FORBIDDEN: Shared secrets or static credentials
- FORBIDDEN: Service accounts with permanent credentials

**Required Configuration**:
```yaml
authentication:
  provider: keycloak  # REQUIRED: keycloak | okta | azure_ad
  sso_enabled: true   # REQUIRED: true
  mfa_required: true  # REQUIRED: true
  token_lifetime: 15m # REQUIRED: ≤ 15 minutes
  refresh_token_lifetime: 24h
  session_timeout: 30m
```

### MUST IMPLEMENT: Access Control

**Rule 1.4: Role-Based Access Control**
- IMPLEMENT: Minimum roles: Admin, Developer, User, Viewer
- IMPLEMENT: Role definitions based on least privilege principle
- IMPLEMENT: Role assignments managed through identity provider
- IMPLEMENT: Regular access reviews (quarterly minimum)
- FORBIDDEN: All-or-nothing permissions
- FORBIDDEN: Admin access for non-administrative users

**Rule 1.5: Attribute-Based Access Control**
- IMPLEMENT: Fine-grained access control based on user attributes
- IMPLEMENT: Support for OPA (Open Policy Agent) or similar policy engine
- IMPLEMENT: Policy-as-code approach for access control
- REQUIRED: Support for dynamic access decisions

**Rule 1.6: Resource-Level Access Control**
- IMPLEMENT: Tenant isolation for multi-tenant deployments
- IMPLEMENT: Namespace or project-level access control
- IMPLEMENT: Data isolation between tenants
- IMPLEMENT: Enforce access control at ALL data access points
- FORBIDDEN: Cross-tenant data access
- FORBIDDEN: Bypassing access control for any operation

**Required Configuration**:
```yaml
authorization:
  rbac_enabled: true              # REQUIRED: true
  abac_enabled: true              # REQUIRED: true
  policy_engine: opa              # REQUIRED: opa | equivalent
  access_review_frequency: quarterly  # REQUIRED: ≤ quarterly
```

---

## RULE: Data Protection

### MUST IMPLEMENT: Encryption

**Rule 2.1: Encryption in Transit**
- IMPLEMENT: TLS 1.2+ for ALL network communications
- IMPLEMENT: mTLS for ALL service-to-service communication
- IMPLEMENT: Certificate-based authentication
- FORBIDDEN: Unencrypted traffic (even internal)
- FORBIDDEN: TLS < 1.2

**Rule 2.2: Encryption at Rest**
- IMPLEMENT: Full disk encryption (LUKS, dm-crypt, etc.)
- IMPLEMENT: Database encryption for sensitive fields
- IMPLEMENT: Encryption for ALL stored data (prompts, responses, embeddings)
- IMPLEMENT: Key management through dedicated service (Vault, AWS KMS, etc.)
- FORBIDDEN: Unencrypted storage of sensitive data
- FORBIDDEN: Hardcoded encryption keys

**Rule 2.3: Key Management**
- IMPLEMENT: Keys stored in secrets management system (Vault)
- IMPLEMENT: Key rotation every 90 days (maximum)
- IMPLEMENT: Separate keys per environment (dev, staging, prod)
- IMPLEMENT: Hardware Security Modules (HSM) for highly sensitive data
- FORBIDDEN: Keys in code or configuration files
- FORBIDDEN: Key rotation period > 90 days

**Required Configuration**:
```yaml
data_protection:
  encryption:
    in_transit: tls_1_3           # REQUIRED: tls_1_2 | tls_1_3
    at_rest: aes_256              # REQUIRED: aes_256 or stronger
    key_management: vault         # REQUIRED: vault | aws_kms | equivalent
    key_rotation_days: 90         # REQUIRED: ≤ 90 days
```

### MUST IMPLEMENT: PII Handling

**Rule 2.4: PII Detection and Redaction**
- IMPLEMENT: Automatic detection of PII in prompts and responses
- IMPLEMENT: Redaction before logging or analytics
- IMPLEMENT: Configurable redaction rules per data classification
- IMPLEMENT: Support for common PII types (SSN, credit cards, emails, etc.)
- FORBIDDEN: PII in logs without redaction
- FORBIDDEN: PII in analytics without anonymization

**Rule 2.5: Data Classification**
- IMPLEMENT: Classification of ALL data (Public, Internal, Confidential, Restricted)
- IMPLEMENT: Data classification labels in metadata
- IMPLEMENT: Access control based on data classification
- IMPLEMENT: Retention policies based on classification
- REQUIRED: Classification must be set for all data

**Rule 2.6: Data Minimization**
- IMPLEMENT: Collect only necessary data
- IMPLEMENT: Anonymize or pseudonymize where possible
- IMPLEMENT: Automatic data deletion per retention policies
- IMPLEMENT: Right to deletion (GDPR Article 17)
- FORBIDDEN: Collecting data "just in case"

**Required Configuration**:
```yaml
data_protection:
  pii:
    detection_enabled: true       # REQUIRED: true
    redaction_enabled: true       # REQUIRED: true
    classification_required: true # REQUIRED: true
    retention_policies_enabled: true  # REQUIRED: true
```

---

## RULE: Input/Output Security

### MUST IMPLEMENT: Input Validation

**Rule 3.1: Input Sanitization**
- IMPLEMENT: Validation of ALL user inputs
- IMPLEMENT: Protection against injection attacks (prompt injection, SQL injection, etc.)
- IMPLEMENT: Input length limits and validation
- IMPLEMENT: Content filtering for malicious inputs
- FORBIDDEN: Trusting user input without validation
- FORBIDDEN: No input length limits

**Rule 3.2: Prompt Injection Protection**
- IMPLEMENT: Detection and blocking of prompt injection attempts
- IMPLEMENT: User prompt isolation from system prompts
- IMPLEMENT: Input validation and sanitization
- IMPLEMENT: Use of guardrails (NeMo Guardrails, etc.)
- FORBIDDEN: Concatenating user input directly into system prompts
- FORBIDDEN: No prompt injection detection

**Rule 3.3: Rate Limiting**
- IMPLEMENT: Per-user rate limits
- IMPLEMENT: Per-tenant rate limits
- IMPLEMENT: Token budget limits per request
- IMPLEMENT: Adaptive rate limiting based on behavior
- FORBIDDEN: No rate limiting
- FORBIDDEN: Global rate limiting only (must have per-user/tenant)

**Required Configuration**:
```yaml
input_output_security:
  input_validation:
    enabled: true                 # REQUIRED: true
    max_input_length: 10000       # REQUIRED: defined limit
    sanitization_enabled: true    # REQUIRED: true
    injection_protection: true    # REQUIRED: true
  
  rate_limiting:
    per_user_rps: 10              # REQUIRED: defined limit
    per_tenant_rps: 100           # REQUIRED: defined limit
    token_budget_per_request: 4000  # REQUIRED: defined limit
```

### MUST IMPLEMENT: Output Security

**Rule 3.4: Output Filtering**
- IMPLEMENT: Content filtering for inappropriate content
- IMPLEMENT: Guardrails for dangerous content
- IMPLEMENT: Bias detection and mitigation
- IMPLEMENT: Toxicity filtering
- FORBIDDEN: Delivering unfiltered model outputs
- FORBIDDEN: No content moderation

**Rule 3.5: Response Validation**
- IMPLEMENT: Validation of output format
- IMPLEMENT: Check for hallucinations or fabricated information
- IMPLEMENT: Content moderation before delivery
- IMPLEMENT: Metadata tagging for audit purposes
- REQUIRED: Validate before delivery to users

**Rule 3.6: Data Leakage Prevention**
- IMPLEMENT: Monitor outputs for sensitive data
- IMPLEMENT: Block unauthorized data exfiltration
- IMPLEMENT: Real-time DLP scanning
- IMPLEMENT: Integration with DLP systems (Curie Proxy, etc.)
- FORBIDDEN: No DLP scanning

**Required Configuration**:
```yaml
input_output_security:
  output_filtering:
    guardrails_enabled: true      # REQUIRED: true
    content_filtering: true       # REQUIRED: true
    dlp_enabled: true             # REQUIRED: true
```

---

## RULE: Network Security

### MUST IMPLEMENT: Zero-Trust Networking

**Rule 4.1: Service Mesh**
- IMPLEMENT: Service mesh for ALL microservices (Istio, Linkerd, etc.)
- IMPLEMENT: mTLS for ALL service-to-service communication
- IMPLEMENT: Network policies (Kubernetes NetworkPolicies, Cilium)
- IMPLEMENT: No direct service-to-service access without authentication
- FORBIDDEN: Direct service-to-service communication without mTLS
- FORBIDDEN: Services without network policies

**Rule 4.2: API Gateway**
- IMPLEMENT: ALL external traffic through API gateway
- IMPLEMENT: Rate limiting at gateway level
- IMPLEMENT: Request validation and transformation
- IMPLEMENT: Authentication and authorization at gateway
- FORBIDDEN: Direct external access to services
- FORBIDDEN: Bypassing API gateway

**Rule 4.3: Network Segmentation**
- IMPLEMENT: Separation of public, DMZ, and internal zones
- IMPLEMENT: Firewall rules at ALL network boundaries
- IMPLEMENT: No direct internet access from internal services
- IMPLEMENT: Egress traffic control and monitoring
- FORBIDDEN: Internal services with direct internet access

**Required Configuration**:
```yaml
network_security:
  service_mesh:
    enabled: true                 # REQUIRED: true
    provider: istio               # REQUIRED: istio | linkerd | equivalent
    mtls_enforced: true           # REQUIRED: true
    
  api_gateway:
    enabled: true                 # REQUIRED: true
    rate_limiting: true           # REQUIRED: true
    waf_enabled: true             # REQUIRED: true
```

### MUST IMPLEMENT: Threat Protection

**Rule 4.4: Web Application Firewall**
- IMPLEMENT: WAF at ingress (ModSecurity, etc.)
- IMPLEMENT: Protection against OWASP Top 10
- IMPLEMENT: Custom rules for LLM-specific threats
- IMPLEMENT: Regular rule updates
- FORBIDDEN: No WAF at ingress

**Rule 4.5: DDoS Protection**
- IMPLEMENT: Rate limiting at multiple layers
- IMPLEMENT: Connection limits per IP
- IMPLEMENT: Distributed rate limiting
- IMPLEMENT: Integration with CDN or cloud DDoS protection
- FORBIDDEN: No DDoS protection

**Rule 4.6: Intrusion Detection**
- IMPLEMENT: Runtime security monitoring (Falco, KubeArmor)
- IMPLEMENT: Anomaly detection for unusual behavior
- IMPLEMENT: Integration with SIEM for alerting
- IMPLEMENT: Automated threat response
- REQUIRED: Real-time threat detection

---

## RULE: Threat Protection

### MUST IMPLEMENT: Guardrails

**Rule 5.1: Content Guardrails**
- IMPLEMENT: Filtering of inappropriate or dangerous content
- IMPLEMENT: Bias mitigation
- IMPLEMENT: Toxicity detection
- IMPLEMENT: Use of proven guardrail systems (NeMo Guardrails, etc.)
- FORBIDDEN: No content guardrails
- FORBIDDEN: Custom guardrails without validation

**Rule 5.2: Model Safety**
- IMPLEMENT: Jailbreak detection and prevention
- IMPLEMENT: Prompt injection protection
- IMPLEMENT: Output validation
- IMPLEMENT: Safe model configuration (temperature, top-p, etc.)
- REQUIRED: All model outputs validated before delivery

**Rule 5.3: Adversarial Protection**
- IMPLEMENT: Protection against adversarial prompts
- IMPLEMENT: Input validation and sanitization
- IMPLEMENT: Monitoring for attack patterns
- IMPLEMENT: Automated response to detected attacks
- REQUIRED: Real-time attack detection

**Required Configuration**:
```yaml
threat_protection:
  guardrails:
    enabled: true                 # REQUIRED: true
    provider: nemo_guardrails     # REQUIRED: validated guardrail system
    content_filtering: true       # REQUIRED: true
    bias_mitigation: true         # REQUIRED: true
```

---

## RULE: Security Monitoring

### MUST IMPLEMENT: Security Monitoring

**Rule 6.1: Security Event Logging**
- IMPLEMENT: ALL authentication events logged
- IMPLEMENT: ALL authorization decisions logged
- IMPLEMENT: ALL data access events logged
- IMPLEMENT: Failed access attempts logged
- FORBIDDEN: Security events without logging

**Rule 6.2: SIEM Integration**
- IMPLEMENT: Integration with SIEM for security event correlation
- IMPLEMENT: Real-time alerting on security events
- IMPLEMENT: Automated threat detection
- IMPLEMENT: Security dashboards and reporting
- REQUIRED: Real-time security monitoring

**Rule 6.3: Vulnerability Scanning**
- IMPLEMENT: Regular vulnerability scans (weekly minimum)
- IMPLEMENT: Dependency scanning in CI/CD
- IMPLEMENT: Container image scanning
- IMPLEMENT: Infrastructure-as-Code scanning
- FORBIDDEN: No vulnerability scanning

**Rule 6.4: Runtime Security**
- IMPLEMENT: Runtime security monitoring (Falco, KubeArmor)
- IMPLEMENT: Container runtime security
- IMPLEMENT: File integrity monitoring
- IMPLEMENT: Process anomaly detection
- REQUIRED: Continuous runtime monitoring

**Required Configuration**:
```yaml
security_monitoring:
  logging:
    auth_events: true             # REQUIRED: true
    access_events: true           # REQUIRED: true
    failed_attempts: true         # REQUIRED: true
    
  siem:
    integration_enabled: true     # REQUIRED: true
    alerting_enabled: true        # REQUIRED: true
    
  vulnerability_scanning:
    frequency: weekly             # REQUIRED: ≤ weekly
    ci_cd_integration: true       # REQUIRED: true
    container_scanning: true      # REQUIRED: true
```

---

## RULE: Compliance & Audit

### MUST IMPLEMENT: Audit Logging

**Rule 7.1: Comprehensive Audit Logs**
- IMPLEMENT: ALL user actions logged with user identity
- IMPLEMENT: ALL system changes logged
- IMPLEMENT: ALL data access logged
- IMPLEMENT: Immutable audit logs (write-once storage)
- FORBIDDEN: User actions without audit logging
- FORBIDDEN: Mutable audit logs

**Rule 7.2: Audit Log Storage**
- IMPLEMENT: Centralized log storage (ClickHouse, Elasticsearch, etc.)
- IMPLEMENT: Retention period based on compliance requirements (minimum 1 year)
- IMPLEMENT: Encrypted audit logs
- IMPLEMENT: Tamper-proof log storage
- REQUIRED: Immutable, centralized, encrypted storage

**Rule 7.3: Audit Log Format**
- IMPLEMENT: Structured logging (JSON format)
- IMPLEMENT: Standard fields: timestamp, user, action, resource, result, IP address
- IMPLEMENT: Correlation IDs for request tracing
- IMPLEMENT: Compliance metadata (data classification, etc.)
- FORBIDDEN: Unstructured audit logs

**Required Configuration**:
```yaml
compliance:
  audit_logging:
    enabled: true                 # REQUIRED: true
    retention_days: 365           # REQUIRED: ≥ 365 days (1 year minimum)
    immutable: true               # REQUIRED: true
    centralized_storage: true     # REQUIRED: true
```

---

## VALIDATION CHECKLIST

Before production deployment, verify ALL requirements:

```
AUTHENTICATION & AUTHORIZATION:
[ ] SSO integration implemented and tested
[ ] MFA enforced for all users (no exceptions)
[ ] RBAC implemented with least privilege
[ ] Service accounts use secure authentication (SPIFFE/mTLS)
[ ] Access reviews scheduled (quarterly)

DATA PROTECTION:
[ ] TLS 1.2+ for all communications
[ ] Encryption at rest enabled for all data
[ ] Key management system integrated (Vault)
[ ] PII detection and redaction implemented
[ ] Data classification implemented for all data

INPUT/OUTPUT SECURITY:
[ ] Input validation and sanitization implemented
[ ] Prompt injection protection enabled
[ ] Rate limiting configured (per-user and per-tenant)
[ ] Output filtering and guardrails enabled
[ ] DLP integration for sensitive data

NETWORK SECURITY:
[ ] Service mesh with mTLS enabled
[ ] API gateway with authentication
[ ] Network policies configured
[ ] WAF enabled at ingress
[ ] DDoS protection configured

THREAT PROTECTION:
[ ] Guardrails implemented and tested
[ ] Jailbreak detection enabled
[ ] Security monitoring enabled
[ ] Vulnerability scanning automated (weekly)
[ ] Runtime security monitoring active

COMPLIANCE & AUDIT:
[ ] Audit logging enabled for all actions
[ ] Audit logs stored in centralized, immutable system
[ ] Compliance requirements identified
[ ] Data subject rights implemented (if GDPR applicable)
[ ] Compliance reporting automated
```

---

## ENFORCEMENT

- **Pre-deployment**: Security team MUST review and approve
- **Code Review**: Security requirements MUST be validated
- **Continuous**: Security monitoring MUST be active
- **Regular**: Security assessments MUST be conducted

**Failure to meet requirements = Deployment blocked**
