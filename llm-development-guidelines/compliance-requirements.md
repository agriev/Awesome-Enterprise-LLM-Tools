# Compliance & Governance Requirements - Development Instructions for LLM

**Context**: When developing, reviewing, or deploying enterprise LLM services, you MUST implement all compliance and governance requirements specified in this document. These requirements ensure regulatory compliance and proper data governance.

**Enforcement**: Compliance requirements MUST be validated before production deployment. Services that don't meet compliance requirements cannot be deployed to production.

---

## RULE: Regulatory Compliance

### MUST COMPLY: GDPR (If Processing EU Personal Data)

**Rule 1.1: Lawful Basis for Processing**
- IDENTIFY: Lawful basis (consent, contract, legal obligation, etc.)
- DOCUMENT: Lawful basis for all personal data processing
- IMPLEMENT: Transparency about processing
- REQUIRED: Documented lawful basis for each processing activity
- FORBIDDEN: Processing without lawful basis

**Rule 1.2: Data Subject Rights**
- IMPLEMENT: Right to access (Article 15) - data export
- IMPLEMENT: Right to rectification (Article 16) - data correction
- IMPLEMENT: Right to erasure (Article 17) - data deletion
- IMPLEMENT: Right to restrict processing (Article 18)
- IMPLEMENT: Right to data portability (Article 20) - standard format export
- IMPLEMENT: Right to object (Article 21)
- REQUIRED: All data subject rights implemented
- REQUIRED: Response to requests within 30 days

**Rule 1.3: Data Protection by Design**
- IMPLEMENT: Privacy by design principles
- IMPLEMENT: Data minimization (collect only necessary)
- IMPLEMENT: Purpose limitation
- IMPLEMENT: Storage limitation
- REQUIRED: Privacy considerations in all design decisions

**Rule 1.4: Data Protection Impact Assessment**
- CONDUCT: DPIA for high-risk processing
- DOCUMENT: DPIA results
- IMPLEMENT: Mitigation measures from DPIA
- REQUIRED: DPIA before high-risk processing

**Required Configuration**:
```yaml
gdpr_compliance:
  enabled: true                   # REQUIRED: if processing EU data
  lawful_basis:
    - consent                    # REQUIRED: documented basis
    - contract
  data_subject_rights:
    access_enabled: true         # REQUIRED: true
    rectification_enabled: true  # REQUIRED: true
    erasure_enabled: true        # REQUIRED: true
    portability_enabled: true    # REQUIRED: true
  response_days: 30              # REQUIRED: ≤ 30 days
```

### MUST COMPLY: HIPAA (If Processing Healthcare Data)

**Rule 1.5: Administrative Safeguards**
- DESIGNATE: Security officer
- IMPLEMENT: Workforce training
- IMPLEMENT: Access management
- IMPLEMENT: Contingency planning
- REQUIRED: Security officer designated
- REQUIRED: Workforce training documented

**Rule 1.6: Physical Safeguards**
- IMPLEMENT: Facility access controls
- IMPLEMENT: Workstation security
- IMPLEMENT: Device controls
- IMPLEMENT: Media controls
- REQUIRED: Physical security controls

**Rule 1.7: Technical Safeguards**
- IMPLEMENT: Access control (unique user identification)
- IMPLEMENT: Audit controls (logging and monitoring)
- IMPLEMENT: Integrity controls (data protection)
- IMPLEMENT: Transmission security (encryption)
- REQUIRED: All technical safeguards implemented

**Required Configuration**:
```yaml
hipaa_compliance:
  enabled: true                   # REQUIRED: if processing healthcare data
  safeguards:
    administrative: true         # REQUIRED: true
    physical: true              # REQUIRED: true
    technical: true             # REQUIRED: true
  encryption_required: true      # REQUIRED: true
  audit_logging_required: true   # REQUIRED: true
```

### MUST ALIGN: SOC 2 (For Enterprise Deployments)

**Rule 1.8: Security Controls**
- IMPLEMENT: Access controls
- IMPLEMENT: Encryption
- IMPLEMENT: Network security
- IMPLEMENT: Incident response
- REQUIRED: SOC 2 security controls

**Rule 1.9: Availability Controls**
- IMPLEMENT: System monitoring
- IMPLEMENT: Performance monitoring
- IMPLEMENT: Disaster recovery
- IMPLEMENT: Backup procedures
- REQUIRED: SOC 2 availability controls

**Rule 1.10: Processing Integrity**
- IMPLEMENT: Data validation
- IMPLEMENT: Error handling
- IMPLEMENT: Quality assurance
- IMPLEMENT: Monitoring
- REQUIRED: SOC 2 processing integrity controls

**Rule 1.11: Confidentiality Controls**
- IMPLEMENT: Data classification
- IMPLEMENT: Access restrictions
- IMPLEMENT: Encryption
- IMPLEMENT: Non-disclosure agreements
- REQUIRED: SOC 2 confidentiality controls

**Rule 1.12: Privacy Controls**
- IMPLEMENT: Data collection practices
- IMPLEMENT: Data use and retention
- IMPLEMENT: Data access
- IMPLEMENT: Data disposal
- REQUIRED: SOC 2 privacy controls

**Required Configuration**:
```yaml
soc2_compliance:
  enabled: true                   # REQUIRED: for enterprise deployments
  controls:
    security: true               # REQUIRED: true
    availability: true           # REQUIRED: true
    processing_integrity: true   # REQUIRED: true
    confidentiality: true        # REQUIRED: true
    privacy: true                # REQUIRED: true
```

### MUST ALIGN: ISO 27001 (For Certified Environments)

**Rule 1.13: Information Security Management System**
- IMPLEMENT: Security policies
- IMPLEMENT: Risk management
- IMPLEMENT: Security controls
- IMPLEMENT: Continuous improvement
- REQUIRED: ISMS implemented

**Rule 1.14: Security Controls**
- IMPLEMENT: Access control (A.9)
- IMPLEMENT: Cryptography (A.10)
- IMPLEMENT: Operations security (A.12)
- IMPLEMENT: Communications security (A.13)
- REQUIRED: ISO 27001 controls implemented

**Rule 1.15: Compliance**
- IMPLEMENT: Legal and regulatory compliance (A.18)
- CONDUCT: Compliance reviews
- CONDUCT: Security assessments
- REQUIRED: Regular compliance reviews

---

## RULE: Data Governance

### MUST IMPLEMENT: Data Classification

**Rule 2.1: Data Classification Levels**
- CLASSIFY: Public - No restrictions
- CLASSIFY: Internal - Internal use only
- CLASSIFY: Confidential - Limited access, encryption required
- CLASSIFY: Restricted - Highly restricted, additional controls
- REQUIRED: ALL data classified
- FORBIDDEN: Unclassified data

**Rule 2.2: Classification Labeling**
- LABEL: All data with classification
- STORE: Classification in metadata
- ENFORCE: Classification-based access control
- APPLY: Classification-based retention policies
- REQUIRED: Classification labels in all data metadata

**Rule 2.3: Data Inventory**
- MAINTAIN: Inventory of all data assets
- MAP: Data flows
- TRACK: Data lineage
- REVIEW: Data inventory regularly (quarterly)
- REQUIRED: Complete data inventory

**Required Configuration**:
```yaml
data_governance:
  classification:
    enabled: true                 # REQUIRED: true
    levels:                      # REQUIRED: defined levels
      - public
      - internal
      - confidential
      - restricted
  inventory:
    enabled: true                # REQUIRED: true
    review_frequency: quarterly  # REQUIRED: ≤ quarterly
```

### MUST ENSURE: Data Quality

**Rule 2.4: Data Accuracy**
- IMPLEMENT: Data validation rules
- IMPLEMENT: Data quality checks
- IMPLEMENT: Error detection and correction
- IMPLEMENT: Data quality metrics
- REQUIRED: Data validation for all inputs

**Rule 2.5: Data Completeness**
- IMPLEMENT: Required field validation
- IMPLEMENT: Completeness checks
- IMPLEMENT: Missing data handling
- IMPLEMENT: Data completeness metrics
- REQUIRED: Completeness validation

**Rule 2.6: Data Consistency**
- IMPLEMENT: Data standardization
- IMPLEMENT: Consistency checks
- IMPLEMENT: Conflict resolution
- IMPLEMENT: Data consistency metrics
- REQUIRED: Consistency validation

### MUST TRACK: Data Lineage

**Rule 2.7: Data Flow Tracking**
- TRACK: Source of data
- TRACK: Data transformations
- TRACK: Data destinations
- TRACK: Data usage
- REQUIRED: End-to-end data lineage tracking

**Rule 2.8: Data Lineage Documentation**
- DOCUMENT: Data lineage
- AUTOMATE: Lineage tracking
- VISUALIZE: Lineage
- REPORT: Lineage reporting
- REQUIRED: Documented data lineage

---

## RULE: Privacy Requirements

### MUST IMPLEMENT: Privacy by Design

**Rule 3.1: Privacy Principles**
- IMPLEMENT: Proactive not reactive privacy
- IMPLEMENT: Privacy as the default
- IMPLEMENT: Full functionality with privacy
- IMPLEMENT: End-to-end security
- IMPLEMENT: Visibility and transparency
- IMPLEMENT: Respect for user privacy
- REQUIRED: Privacy by design in all features

**Rule 3.2: Data Minimization**
- IMPLEMENT: Collect only necessary data
- IMPLEMENT: Use data only for intended purpose
- IMPLEMENT: Retain data only as long as necessary
- IMPLEMENT: Delete data when no longer needed
- FORBIDDEN: Collecting data "just in case"

**Rule 3.3: Anonymization & Pseudonymization**
- IMPLEMENT: Anonymize data where possible
- IMPLEMENT: Pseudonymize data for privacy
- ASSESS: Re-identification risk
- IMPLEMENT: Privacy-preserving techniques
- REQUIRED: Privacy-preserving techniques used

### MUST IMPLEMENT: Consent Management

**Rule 3.4: Consent Collection**
- IMPLEMENT: Explicit consent for personal data
- IMPLEMENT: Consent granularity (purpose-specific)
- IMPLEMENT: Easy consent withdrawal
- IMPLEMENT: Consent records
- REQUIRED: Explicit consent for all personal data
- FORBIDDEN: Implied consent

**Rule 3.5: Consent Management**
- IMPLEMENT: Consent storage and tracking
- IMPLEMENT: Consent versioning
- IMPLEMENT: Consent expiry handling
- IMPLEMENT: Consent withdrawal processing
- REQUIRED: Complete consent lifecycle management

**Required Configuration**:
```yaml
privacy:
  consent_management:
    enabled: true                 # REQUIRED: true
    explicit_consent: true       # REQUIRED: true
    granularity: purpose_specific # REQUIRED: purpose-specific
    withdrawal_enabled: true     # REQUIRED: true
  data_minimization:
    enabled: true                # REQUIRED: true
```

### MUST PROTECT: PII

**Rule 3.6: PII Detection**
- IMPLEMENT: Automatic PII detection
- IMPLEMENT: PII classification
- MAINTAIN: PII inventory
- ASSESS: PII risk
- REQUIRED: Automatic PII detection

**Rule 3.7: PII Handling**
- IMPLEMENT: PII encryption
- IMPLEMENT: PII access control
- IMPLEMENT: PII redaction in logs
- IMPLEMENT: PII deletion on request
- REQUIRED: Special handling for all PII

---

## RULE: Audit Requirements

### MUST LOG: Audit Events

**Rule 4.1: Access Events**
- LOG: All data access
- LOG: Authentication events
- LOG: Authorization decisions
- LOG: Failed access attempts
- REQUIRED: All access events logged

**Rule 4.2: Data Operations**
- LOG: Data creation
- LOG: Data modification
- LOG: Data deletion
- LOG: Data export
- REQUIRED: All data operations logged

**Rule 4.3: System Changes**
- LOG: Configuration changes
- LOG: Permission changes
- LOG: Security policy changes
- LOG: System updates
- REQUIRED: All system changes logged

**Rule 4.4: Security Events**
- LOG: Security incidents
- LOG: Policy violations
- LOG: Anomalous behavior
- LOG: Threat detection
- REQUIRED: All security events logged

### MUST MAINTAIN: Audit Log Standards

**Rule 4.5: Audit Log Format**
- FORMAT: Structured format (JSON)
- INCLUDE: Standard fields (timestamp, user, action, resource, result)
- INCLUDE: Correlation IDs
- ENSURE: Immutable logs
- REQUIRED: Structured, immutable audit logs

**Rule 4.6: Audit Log Storage**
- STORE: Centralized storage
- ENCRYPT: Audit logs
- ENSURE: Tamper-proof storage
- RETAIN: Minimum 1 year, 7 years for regulated industries
- REQUIRED: Centralized, encrypted, tamper-proof storage

**Rule 4.7: Audit Log Access**
- RESTRICT: Access (read-only)
- LOG: Audit log access
- PROVIDE: Audit log analysis tools
- ENABLE: Compliance reporting
- REQUIRED: Restricted access with logging

**Required Configuration**:
```yaml
audit_logging:
  enabled: true                   # REQUIRED: true
  format: json                   # REQUIRED: json
  retention_days: 365            # REQUIRED: ≥ 365 (1 year minimum)
  immutable: true                # REQUIRED: true
  centralized_storage: true      # REQUIRED: true
  encrypted: true                # REQUIRED: true
```

### MUST PROVIDE: Compliance Reporting

**Rule 4.8: Regular Reports**
- PROVIDE: Monthly compliance reports
- CONDUCT: Quarterly compliance assessments
- CONDUCT: Annual compliance audits
- PROVIDE: Ad-hoc compliance reports
- REQUIRED: Regular compliance reporting

**Rule 4.9: Compliance Metrics**
- TRACK: Data subject requests processed
- TRACK: Data breaches (if any)
- TRACK: Policy violations
- TRACK: Access reviews completed
- REQUIRED: Compliance metrics tracked

---

## RULE: Access Control Requirements

### MUST IMPLEMENT: Access Management

**Rule 5.1: Access Control**
- IMPLEMENT: Role-based access control (RBAC)
- IMPLEMENT: Attribute-based access control (ABAC)
- IMPLEMENT: Principle of least privilege
- IMPLEMENT: Separation of duties
- REQUIRED: Multi-level access control

**Rule 5.2: Access Reviews**
- CONDUCT: Regular access reviews (quarterly)
- AUTOMATE: Access reviews where possible
- CERTIFY: Access certification
- REVOKE: Access for inactive users
- REQUIRED: Quarterly access reviews

**Rule 5.3: Privileged Access**
- IMPLEMENT: Privileged access management
- IMPLEMENT: Just-in-time access
- IMPLEMENT: Privileged session monitoring
- CONDUCT: Privileged access reviews
- REQUIRED: Special handling for privileged access

### MUST ENFORCE: Data Access Control

**Rule 5.4: Row-Level Security**
- IMPLEMENT: Tenant-based data isolation
- IMPLEMENT: User-based data access
- IMPLEMENT: Attribute-based data filtering
- IMPLEMENT: Dynamic access policies
- REQUIRED: Fine-grained data access control

**Rule 5.5: Data Access Logging**
- LOG: All data access
- MONITOR: Access patterns
- DETECT: Anomalous access
- GENERATE: Access reports
- REQUIRED: Complete data access logging

---

## RULE: Data Retention & Deletion

### MUST IMPLEMENT: Data Retention Policies

**Rule 6.1: Retention Policies**
- DEFINE: Retention period by data classification
- DEFINE: Retention period by data type
- DEFINE: Retention period by regulatory requirement
- ENFORCE: Automated retention enforcement
- REQUIRED: Retention policies for all data

**Rule 6.2: Retention Management**
- IMPLEMENT: Data lifecycle management
- IMPLEMENT: Automated data archival
- CONDUCT: Data retention reviews
- HANDLE: Retention policy exceptions
- REQUIRED: Automated retention management

### MUST SUPPORT: Data Deletion

**Rule 6.3: Automated Deletion**
- IMPLEMENT: Automated deletion per retention policy
- IMPLEMENT: Secure data deletion
- VERIFY: Deletion
- LOG: Deletion events
- REQUIRED: Automated, secure deletion

**Rule 6.4: Right to Deletion**
- IMPLEMENT: Data subject deletion requests
- COMPLETE: Deletion within 30 days (GDPR)
- VERIFY: Deletion
- HANDLE: Deletion exceptions
- REQUIRED: Right to deletion support

**Rule 6.5: Secure Deletion**
- IMPLEMENT: Cryptographic erasure where possible
- IMPLEMENT: Physical destruction for sensitive data
- DELETE: From backups
- VERIFY: Deletion
- REQUIRED: Secure deletion with verification

**Required Configuration**:
```yaml
data_retention:
  policies:
    enabled: true                # REQUIRED: true
    classification_based: true   # REQUIRED: true
    automated_enforcement: true  # REQUIRED: true
  deletion:
    automated: true              # REQUIRED: true
    secure: true                 # REQUIRED: true
    verification: true           # REQUIRED: true
  right_to_deletion:
    enabled: true                # REQUIRED: true (if GDPR)
    response_days: 30           # REQUIRED: ≤ 30 days
```

---

## VALIDATION CHECKLIST

Before production deployment, verify ALL requirements:

```
REGULATORY COMPLIANCE:
[ ] GDPR compliance (if processing EU data) - all data subject rights implemented
[ ] HIPAA compliance (if processing healthcare data) - all safeguards implemented
[ ] SOC 2 alignment (for enterprise deployments) - all controls implemented
[ ] ISO 27001 alignment (for certified environments) - ISMS implemented
[ ] Other regulatory requirements identified and addressed

DATA GOVERNANCE:
[ ] Data classification implemented (ALL data classified)
[ ] Data inventory maintained (complete inventory)
[ ] Data quality measures implemented (accuracy, completeness, consistency)
[ ] Data lineage tracked (end-to-end tracking)
[ ] Data governance policies documented

PRIVACY:
[ ] Privacy by design principles implemented
[ ] Data minimization practices (collect only necessary)
[ ] Consent management implemented (explicit, granular)
[ ] PII protection implemented (detection, encryption, redaction)
[ ] Privacy impact assessments conducted (if high-risk)

AUDIT:
[ ] Audit logging implemented (ALL events logged)
[ ] Audit logs stored securely (centralized, encrypted, immutable)
[ ] Compliance reporting automated
[ ] Audit log access controlled (restricted, logged)

ACCESS CONTROL:
[ ] Access control implemented (RBAC/ABAC)
[ ] Access reviews scheduled (quarterly)
[ ] Data access control enforced (row-level security)
[ ] Privileged access managed (just-in-time, monitored)

DATA RETENTION & DELETION:
[ ] Data retention policies defined (by classification/type/regulation)
[ ] Automated deletion implemented (per retention policy)
[ ] Right to deletion supported (GDPR Article 17)
[ ] Secure deletion implemented (with verification)
```

---

## ENFORCEMENT

- **Pre-deployment**: Compliance requirements MUST be validated
- **Regular**: Compliance assessments MUST be conducted
- **Continuous**: Compliance monitoring MUST be active
- **Failure to meet requirements = Deployment blocked**
