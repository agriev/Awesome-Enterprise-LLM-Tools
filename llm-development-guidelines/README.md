# Enterprise LLM Service Development Instructions

**Context**: This document provides mandatory development instructions for building enterprise-grade LLM services. When developing, maintaining, or reviewing LLM services, you MUST follow all requirements specified in these instructions.

**Purpose**: Ensure all LLM services meet enterprise standards for security, observability, scalability, compliance, and reliability before production deployment.

## How to Use These Instructions

1. **Before Starting Development**: Read all instruction files to understand requirements
2. **During Development**: Reference specific instruction files as you implement features
3. **Before Deployment**: Verify all requirements are met using the checklists
4. **During Code Review**: Use these instructions as validation criteria

## Instruction Files

### 🔒 [Security Requirements](./security-requirements.md)
**MANDATORY**: All LLM services MUST implement security requirements before production deployment.

**When to reference**: 
- Designing authentication and authorization
- Implementing data protection
- Handling user inputs and outputs
- Configuring network security
- Setting up threat protection

### 📊 [Observability Requirements](./observability-requirements.md)
**MANDATORY**: All LLM services MUST implement observability from day one.

**When to reference**:
- Setting up metrics collection
- Implementing logging
- Adding distributed tracing
- Configuring alerting
- Creating dashboards

### ⚡ [Scalability & Performance Requirements](./scalability-requirements.md)
**MANDATORY**: All LLM services MUST meet performance targets and support scalability.

**When to reference**:
- Defining performance SLAs
- Designing for horizontal scaling
- Implementing load balancing
- Setting up caching
- Planning capacity

### ✅ [Compliance & Governance Requirements](./compliance-requirements.md)
**MANDATORY**: All LLM services MUST comply with applicable regulations and governance requirements.

**When to reference**:
- Processing personal data (GDPR)
- Handling healthcare data (HIPAA)
- Implementing data governance
- Setting up audit logging
- Configuring data retention

## Quick Validation Checklist

Before deploying any LLM service to production, verify:

```
SECURITY:
[ ] Authentication: SSO + MFA implemented
[ ] Authorization: RBAC/ABAC implemented
[ ] Encryption: TLS 1.2+ in transit, encryption at rest
[ ] Input Validation: All inputs validated and sanitized
[ ] Output Filtering: Guardrails and content filtering enabled
[ ] Network: Service mesh + API gateway configured
[ ] Monitoring: Security events logged and alerted

OBSERVABILITY:
[ ] Metrics: System, application, and business metrics exposed
[ ] Logging: Structured JSON logs with correlation IDs
[ ] Tracing: Distributed tracing implemented (OpenTelemetry)
[ ] Alerting: Critical, warning, and info alerts configured
[ ] Dashboards: Service overview and performance dashboards created

SCALABILITY:
[ ] Performance: Latency SLAs defined and monitored
[ ] Scaling: Horizontal auto-scaling configured
[ ] Load Balancing: Load balancer with health checks
[ ] Caching: Response and model output caching implemented
[ ] Capacity: Capacity planning completed

COMPLIANCE:
[ ] Data Classification: All data classified
[ ] Audit Logging: All actions logged with user identity
[ ] Data Rights: Data subject rights implemented (GDPR)
[ ] Retention: Data retention policies defined
[ ] Compliance: Applicable regulations identified and addressed
```

## Instruction Format

Each instruction file follows this structure:

1. **MANDATORY Requirements**: What MUST be implemented (non-negotiable)
2. **Implementation Rules**: Specific rules for how to implement
3. **Configuration Examples**: YAML/JSON examples showing required configuration
4. **Validation Checklist**: Items to verify before deployment
5. **Standards Reference**: Industry standards and frameworks aligned with

## Enforcement

- **Pre-deployment**: All requirements must be verified before production deployment
- **Code Review**: Use instruction files as validation criteria during code review
- **Security Review**: Security team validates security requirements implementation
- **Compliance Review**: Compliance team validates compliance requirements implementation
- **Performance Testing**: Performance requirements must be validated through load testing

## Related Documentation

These instructions complement:
- [Reference Architectures](../reference-architectures/) - Implementation patterns
- [Security Best Practices](../security-best-practices.md) - Detailed security guidelines
- [Performance Benchmarks](../performance-benchmarks.md) - Performance targets

## Standards Alignment

All requirements align with:
- **Security**: ISO 27001, NIST Cybersecurity Framework, OWASP Top 10 for LLM
- **Observability**: OpenTelemetry, Prometheus, Grafana Labs best practices
- **Performance**: ITIL 4 service management SLAs
- **Compliance**: GDPR, HIPAA, SOC 2, ISO 22301, NIST SP 800-34
