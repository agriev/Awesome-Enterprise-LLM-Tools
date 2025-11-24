# Data Governance for Enterprise LLM

Comprehensive data governance framework for LLM deployments in enterprise environments, aligned with industry standards and best practices.

## TL;DR

Data governance for LLM isn't just about compliance - it's about making sure your data is trustworthy, secure, and actually useful. This framework gives you a practical roadmap from basic data management to enterprise-grade governance. We've aligned it with industry standards (DAMA-DMBOK, COBIT, ISO) so you can speak the same language as auditors and executives.

**Key Takeaways**:
- Start with data classification - you can't protect what you don't know you have
- Metadata management is your foundation - use OpenMetadata or DataHub
- Data quality isn't optional - bad data in means bad results out
- Lineage tracking saves you when things break - you'll thank us later
- This is a journey, not a destination - plan for 12-24 months to reach maturity

## Overview

Data governance for LLM systems is about making sure your data works for you, not against you. It covers policies, processes, and technologies to ensure data quality, security, compliance, and effective data management throughout the LLM lifecycle. We've aligned this framework with industry standards (DAMA-DMBOK, COBIT, ISO/IEC 38500) so it fits into your existing governance programs.

## Data Governance Framework

### 1. Data Governance Principles

#### Core Principles

These aren't just buzzwords - they're the foundation of good data governance:

- **Accountability**: Someone needs to own each data asset. When something breaks, you need to know who to call.
- **Transparency**: Your data processes should be clear and documented. No black boxes - if you can't explain it, you can't trust it.
- **Standards**: Consistent data standards mean your analysts don't waste time figuring out what "customer_id" means in each system.
- **Quality**: Bad data in means bad results out. Quality isn't optional - it's the price of admission.
- **Security**: Protect your data like it's your company's crown jewels (because it often is).
- **Compliance**: Regulatory requirements aren't going away. Build compliance in from the start, not as an afterthought.
- **Value**: Data governance should enable value, not block it. If it's only slowing things down, you're doing it wrong.

#### Alignment with Standards
- **DAMA-DMBOK 2.0**: Data Management Body of Knowledge
- **COBIT 2019**: Control Objectives for Information and Related Technologies
- **ISO/IEC 38500**: IT Governance
- **DCAM**: Data Management Capability Assessment Model

### 2. Data Governance Organization

#### Roles and Responsibilities

**Data Governance Council**
This is your steering committee - the people who make decisions and break ties. They set strategic direction, approve policies, and make sure governance aligns with business goals. Without this, governance becomes a paper exercise.

**Data Stewards**
These are your domain experts - the people who actually know what the data means. They own data quality, define business rules, and help users understand how to use data correctly. They're your first line of defense against bad data.

**Data Architects**
These folks design the technical foundation. They choose tools, design integration patterns, and make sure everything fits together. They're the ones who make governance actually work in practice.

**Data Engineers**
- Data pipeline implementation
- Data quality automation
- Infrastructure management
- Performance optimization

**Security & Compliance Officers**
- Security policy enforcement
- Compliance monitoring and reporting
- Risk assessment and mitigation
- Audit coordination

### 3. Data Classification and Cataloging

#### Data Classification Framework

**Classification Levels**
- **Public**: No restrictions, can be freely shared
- **Internal**: Restricted to organization, no external sharing
- **Confidential**: Restricted access, requires authorization
- **Restricted**: Highly sensitive, strict access controls
- **Top Secret**: Maximum security, minimal access

**Classification Criteria**
- Sensitivity level (PII, financial, health data)
- Regulatory requirements (GDPR, HIPAA, PCI-DSS)
- Business impact of disclosure
- Data retention requirements

#### Data Catalog

**Metadata Management**
- **Technical Metadata**: Schema, data types, sources, transformations
- **Business Metadata**: Business definitions, ownership, usage
- **Operational Metadata**: Lineage, quality metrics, access logs
- **Governance Metadata**: Classification, policies, compliance status

**Catalog Components**
- **Data Dictionary**: Comprehensive data definitions
- **Business Glossary**: Business terms and definitions
- **Data Lineage**: End-to-end data flow tracking
- **Impact Analysis**: Change impact assessment

**Open Source Metadata Management Tools**

#### OpenMetadata
- **Description**: Open-source unified metadata platform
- **Enterprise Readiness**: ⭐⭐⭐⭐ (High)
- **Key Features**: 
  - Unified metadata management (data, ML, pipelines)
  - Built-in data quality and profiling
  - End-to-end lineage tracking
  - REST APIs and SDKs
  - Role-based access control
  - Integration with 50+ data sources
- **GitHub**: https://github.com/open-metadata/OpenMetadata
- **Enterprise Considerations**:
  - ✅ Native RBAC with fine-grained permissions
  - ✅ SSO integration (SAML, OAuth, LDAP)
  - ✅ Audit logging and compliance features
  - ✅ High availability and scalability
  - ⚠️ Requires PostgreSQL/MySQL for metadata storage
  - ⚠️ Elasticsearch needed for search (can be resource-intensive)
- **Recommendations for Large Enterprise**:
  - Deploy with high-availability PostgreSQL cluster
  - Use managed Elasticsearch or OpenSearch for production
  - Implement SSO integration from day one
  - Set up automated backups for metadata
  - Configure RBAC policies before production use
  - Monitor Elasticsearch cluster performance

#### DataHub
- **Description**: LinkedIn's open-source metadata platform
- **Enterprise Readiness**: ⭐⭐⭐⭐ (High)
- **Key Features**:
  - Real-time metadata ingestion
  - GraphQL API for metadata access
  - Data lineage visualization
  - Search and discovery
  - Tagging and ownership
- **GitHub**: https://github.com/datahub-project/datahub
- **Enterprise Considerations**:
  - ✅ Scalable architecture (Kafka-based ingestion)
  - ✅ GraphQL API for integrations
  - ✅ Role-based access control
  - ⚠️ Complex deployment (Kafka, Elasticsearch, MySQL)
  - ⚠️ Requires significant infrastructure resources
  - ⚠️ RBAC is available but may need customization
- **Recommendations for Large Enterprise**:
  - Use managed Kafka for production (AWS MSK, Confluent Cloud)
  - Deploy with Kubernetes for scalability
  - Implement external authentication (OIDC/SAML)
  - Set up monitoring for all components (Kafka, Elasticsearch, MySQL)
  - Plan for horizontal scaling of Elasticsearch
  - Consider DataHub Cloud for managed service

#### Amundsen
- **Description**: Lyft's open-source data discovery and metadata engine
- **Enterprise Readiness**: ⭐⭐⭐ (Medium-High)
- **Key Features**:
  - Data discovery and search
  - Column-level lineage
  - Usage statistics
  - User feedback and ratings
- **GitHub**: https://github.com/amundsen-io/amundsen
- **Enterprise Considerations**:
  - ✅ Lightweight and fast
  - ✅ Good search capabilities
  - ⚠️ Limited RBAC (requires custom implementation)
  - ⚠️ Lineage support is basic
  - ⚠️ Requires Elasticsearch and Neo4j
- **Recommendations for Large Enterprise**:
  - Implement custom RBAC layer (OPA, custom middleware)
  - Integrate with enterprise SSO (Keycloak, Okta)
  - Use managed Neo4j or Neo4j Aura for graph database
  - Add audit logging layer
  - Consider Amundsen Plus (commercial) for enterprise features

#### Apache Atlas
- **Description**: Hadoop ecosystem metadata and governance framework
- **Enterprise Readiness**: ⭐⭐⭐ (Medium)
- **Key Features**:
  - Metadata management
  - Data classification and tagging
  - Lineage tracking
  - Policy engine
- **GitHub**: https://github.com/apache/atlas
- **Enterprise Considerations**:
  - ✅ Strong lineage capabilities
  - ✅ Policy engine for governance
  - ⚠️ Tightly coupled with Hadoop ecosystem
  - ⚠️ Complex deployment and configuration
  - ⚠️ Limited modern data source connectors
- **Recommendations for Large Enterprise**:
  - Best for organizations with existing Hadoop infrastructure
  - Requires significant operational expertise
  - Consider migration to OpenMetadata or DataHub for modern stack
  - If using, integrate with Ranger for enhanced security

#### Marquez
- **Description**: Open-source metadata service for data pipelines
- **Enterprise Readiness**: ⭐⭐ (Medium)
- **Key Features**:
  - Job and dataset metadata
  - Data lineage
  - Job run tracking
- **GitHub**: https://github.com/MarquezProject/marquez
- **Enterprise Considerations**:
  - ✅ Simple and lightweight
  - ✅ Good for pipeline metadata
  - ⚠️ Limited data catalog features
  - ⚠️ No built-in RBAC
  - ⚠️ Focused on pipeline metadata only
- **Recommendations for Large Enterprise**:
  - Use as complementary tool for pipeline metadata
  - Integrate with OpenMetadata or DataHub for full catalog
  - Add custom authentication layer
  - Suitable for DevOps-focused teams

**Commercial Metadata Platforms** (Reference)
- **Collibra**: Enterprise data governance platform (commercial)
- **Alation**: Data catalog and governance platform (commercial)
- **Informatica EDC**: Enterprise data catalog (commercial)
- **Talend Data Catalog**: Data catalog solution (commercial)

### 4. Data Quality Management

#### Data Quality Dimensions

**Completeness**: Percentage of data fields populated
- **Target**: >95% for critical fields
- **Measurement**: Count of null/missing values
- **Remediation**: Data enrichment, validation rules

**Accuracy**: Data correctness and validity
- **Target**: >99% accuracy for critical data
- **Measurement**: Validation against authoritative sources
- **Remediation**: Data correction, source validation

**Consistency**: Data consistency across systems
- **Target**: 100% consistency for key attributes
- **Measurement**: Cross-system comparison
- **Remediation**: Standardization, reconciliation

**Timeliness**: Data freshness and availability
- **Target**: Real-time or near-real-time for critical data
- **Measurement**: Time from source to availability
- **Remediation**: Pipeline optimization, caching

**Validity**: Data conforms to defined rules
- **Target**: 100% validity for all data
- **Measurement**: Rule-based validation
- **Remediation**: Data cleansing, rule enforcement

**Uniqueness**: No duplicate records
- **Target**: 0% duplicates for key entities
- **Measurement**: Duplicate detection algorithms
- **Remediation**: Deduplication, merge processes

#### Data Quality Framework

**Quality Assessment**
- Automated quality checks in pipelines
- Regular quality audits and reports
- Quality scorecards and dashboards
- Exception handling and alerting

**Quality Improvement**
- Root cause analysis for quality issues
- Process improvement initiatives
- Data cleansing and enrichment
- Source system improvements

**Quality Monitoring**
- Real-time quality monitoring
- Trend analysis and forecasting
- Quality metrics and KPIs
- Stakeholder reporting

### 5. Data Lineage

#### Lineage Tracking

**End-to-End Lineage**
- Source systems to consumption points
- Transformation logic and business rules
- Data dependencies and relationships
- Impact analysis for changes

**Lineage Types**
- **Technical Lineage**: Code-level lineage (SQL, Python)
- **Business Lineage**: Business process lineage
- **Operational Lineage**: Runtime execution lineage
- **Provenance**: Data origin and history

**Lineage Tools**
- **OpenLineage**: Open-source lineage framework
- **Apache Atlas**: Data lineage and governance
- **Manta**: Automated data lineage platform
- **MANTA**: Data lineage and impact analysis

#### Lineage Use Cases

**Impact Analysis**
- Assess impact of schema changes
- Identify downstream dependencies
- Plan migration and upgrade strategies
- Risk assessment for changes

**Compliance and Auditing**
- Track data flow for compliance
- Audit data access and transformations
- Regulatory reporting support
- Data retention compliance

**Data Quality**
- Trace quality issues to sources
- Understand data transformations
- Identify quality improvement opportunities
- Validate data accuracy

### 6. Data Security and Privacy

#### Data Protection

**Encryption**
- **At Rest**: AES-256 encryption for stored data
- **In Transit**: TLS 1.3 for network communication
- **In Use**: Homomorphic encryption for sensitive computations
- **Key Management**: Centralized key management (Vault, KMS)

**Access Control**
- **Role-Based Access Control (RBAC)**: Role-based permissions
- **Attribute-Based Access Control (ABAC)**: Attribute-based permissions
- **Row-Level Security**: Data-level access control
- **Column-Level Security**: Field-level masking and encryption

**Data Masking and Anonymization**
- **Static Masking**: Mask data in non-production environments
- **Dynamic Masking**: Real-time data masking
- **Anonymization**: Remove PII while preserving utility
- **Pseudonymization**: Replace identifiers with pseudonyms

#### Privacy Management

**PII Handling**
- **Identification**: Automatically identify PII
- **Classification**: Classify PII by sensitivity
- **Protection**: Apply appropriate protection measures
- **Retention**: Manage PII retention and deletion

**Consent Management**
- **Consent Tracking**: Track user consent for data processing
- **Consent Withdrawal**: Support consent withdrawal
- **Right to Deletion**: Implement data deletion requests
- **Data Portability**: Support data export requests

**Privacy by Design**
- **Minimal Data Collection**: Collect only necessary data
- **Purpose Limitation**: Use data only for stated purposes
- **Data Minimization**: Minimize data processing
- **Privacy Impact Assessments**: Regular privacy assessments

### 7. Compliance and Regulatory Requirements

#### Regulatory Frameworks

**GDPR (General Data Protection Regulation)**
- **Data Subject Rights**: Right to access, rectification, erasure
- **Lawful Basis**: Document lawful basis for processing
- **Data Protection Impact Assessments (DPIA)**: Risk assessments
- **Breach Notification**: 72-hour breach notification requirement

**CCPA (California Consumer Privacy Act)**
- **Consumer Rights**: Access, deletion, opt-out rights
- **Disclosure Requirements**: Privacy policy requirements
- **Non-Discrimination**: Prohibition of discrimination
- **Vendor Management**: Third-party data sharing requirements

**HIPAA (Health Insurance Portability and Accountability Act)**
- **Protected Health Information (PHI)**: PHI protection requirements
- **Administrative Safeguards**: Security management processes
- **Physical Safeguards**: Physical access controls
- **Technical Safeguards**: Technical security measures

**SOX (Sarbanes-Oxley Act)**
- **Financial Data Integrity**: Ensure financial data accuracy
- **Access Controls**: Control access to financial systems
- **Audit Trails**: Comprehensive audit logging
- **Change Management**: Control system changes

**PCI-DSS (Payment Card Industry Data Security Standard)**
- **Cardholder Data Protection**: Protect cardholder data
- **Network Security**: Secure network architecture
- **Access Control**: Restrict access to cardholder data
- **Monitoring**: Monitor and test networks

#### Compliance Management

**Compliance Monitoring**
- Automated compliance checks
- Regular compliance audits
- Compliance dashboards and reporting
- Exception management

**Compliance Documentation**
- Policies and procedures documentation
- Compliance evidence collection
- Audit trail maintenance
- Regulatory reporting

### 8. Data Lifecycle Management

#### Lifecycle Stages

**Data Creation**
- Data capture and ingestion
- Initial quality validation
- Classification and cataloging
- Access control setup

**Data Storage**
- Storage tiering (hot, warm, cold)
- Backup and replication
- Retention policy enforcement
- Access monitoring

**Data Usage**
- Access control enforcement
- Usage monitoring and analytics
- Performance optimization
- Quality monitoring

**Data Archival**
- Archive policy definition
- Archive storage selection
- Archive indexing and search
- Archive access management

**Data Deletion**
- Deletion policy enforcement
- Secure data deletion
- Deletion verification
- Compliance with retention requirements

#### Retention Policies

**Policy Definition**
- Regulatory requirements
- Business requirements
- Legal hold requirements
- Storage cost optimization

**Policy Enforcement**
- Automated retention management
- Exception handling
- Audit and compliance
- Regular policy review

### 9. Data Governance Maturity Model

#### Maturity Levels (Based on CMMI)

**Level 1: Initial**
- Ad-hoc data management
- No formal processes
- Reactive problem solving
- Limited documentation

**Level 2: Managed**
- Basic data governance processes
- Documented procedures
- Reactive quality management
- Some automation

**Level 3: Defined**
- Standardized processes
- Proactive quality management
- Data governance organization
- Training and awareness

**Level 4: Quantitatively Managed**
- Measured processes
- Data quality metrics
- Predictive analytics
- Continuous improvement

**Level 5: Optimizing**
- Continuous optimization
- Innovation and experimentation
- Best practice sharing
- Strategic data value realization

#### Assessment Framework

**Assessment Dimensions**
- **Organization**: Governance structure and roles
- **Processes**: Data management processes
- **Technology**: Tools and platforms
- **Data**: Data quality and catalog
- **Compliance**: Regulatory compliance
- **Value**: Business value realization

**Assessment Process**
- Self-assessment questionnaires
- External assessments
- Gap analysis
- Improvement roadmap

### 10. Data Governance Maturity Roadmap

#### Roadmap Overview

This roadmap provides a structured approach to advancing data governance maturity from Level 1 (Initial) to Level 5 (Optimizing), with specific initiatives, timelines, and success criteria for each phase.

#### Level 1 → Level 2: Foundation (Months 1-6)

**Objectives**:
- Establish basic data governance structure
- Document current state
- Implement foundational processes

**Key Initiatives**:
1. **Governance Structure** (Month 1-2)
   - Establish data governance council
   - Define roles and responsibilities
   - Create governance charter
   - Secure executive sponsorship

2. **Policy Framework** (Month 2-3)
   - Define data governance policies
   - Create data classification framework
   - Establish basic data standards
   - Document data management procedures

3. **Basic Catalog** (Month 3-4)
   - Deploy metadata management tool (OpenMetadata or DataHub)
   - Catalog critical data assets
   - Document data sources
   - Create basic data dictionary

4. **Quality Foundation** (Month 4-5)
   - Define data quality dimensions
   - Implement basic quality checks
   - Set up quality monitoring
   - Create quality dashboards

5. **Security Basics** (Month 5-6)
   - Implement data classification
   - Set up basic access controls
   - Establish encryption standards
   - Create security policies

**Success Criteria**:
- Governance council operational
- Policies documented and approved
- 50% of critical data assets cataloged
- Basic quality metrics established
- Security controls implemented

**Tools to Deploy**:
- OpenMetadata or DataHub (metadata catalog)
- Great Expectations (data quality)
- Keycloak (access control)

#### Level 2 → Level 3: Standardization (Months 7-12)

**Objectives**:
- Standardize processes across organization
- Expand data catalog coverage
- Implement comprehensive quality framework
- Establish data lineage

**Key Initiatives**:
1. **Process Standardization** (Month 7-8)
   - Standardize data management processes
   - Create process documentation
   - Implement training programs
   - Establish best practices

2. **Catalog Expansion** (Month 8-9)
   - Expand catalog to 80% of data assets
   - Implement business glossary
   - Add data owners and stewards
   - Create data domain structure

3. **Quality Framework** (Month 9-10)
   - Implement comprehensive quality framework
   - Deploy automated quality checks
   - Set up quality scorecards
   - Establish quality SLAs

4. **Lineage Implementation** (Month 10-11)
   - Deploy lineage tracking (OpenLineage)
   - Map critical data flows
   - Document transformations
   - Create lineage visualizations

5. **Security Enhancement** (Month 11-12)
   - Implement fine-grained access control
   - Deploy data masking for non-production
   - Set up audit logging
   - Conduct security assessments

**Success Criteria**:
- 80% of data assets cataloged
- Quality framework operational
- Lineage for critical data flows
- Security controls comprehensive
- Processes standardized

**Tools to Deploy**:
- OpenLineage (lineage tracking)
- Apache Griffin or Soda (data quality)
- Immuta or Privacera (data security)

#### Level 3 → Level 4: Measurement (Months 13-18)

**Objectives**:
- Implement comprehensive metrics
- Establish data quality SLAs
- Advanced lineage tracking
- Predictive quality management

**Key Initiatives**:
1. **Metrics Framework** (Month 13-14)
   - Define comprehensive metrics
   - Implement metrics collection
   - Create metrics dashboards
   - Establish reporting cadence

2. **Quality SLAs** (Month 14-15)
   - Define quality SLAs by data domain
   - Implement SLA monitoring
   - Set up automated alerts
   - Create quality improvement plans

3. **Advanced Lineage** (Month 15-16)
   - Expand lineage to all data flows
   - Implement impact analysis
   - Create change management process
   - Integrate lineage with catalog

4. **Predictive Quality** (Month 16-17)
   - Implement quality trend analysis
   - Deploy predictive quality models
   - Set up proactive alerts
   - Create quality forecasting

5. **Compliance Automation** (Month 17-18)
   - Automate compliance checks
   - Implement compliance dashboards
   - Set up regulatory reporting
   - Conduct compliance audits

**Success Criteria**:
- Comprehensive metrics framework
- Quality SLAs defined and monitored
- Full lineage coverage
- Predictive quality operational
- Compliance automated

**Tools to Deploy**:
- Custom metrics dashboards (Grafana)
- ML-based quality prediction
- Compliance automation tools

#### Level 4 → Level 5: Optimization (Months 19-24)

**Objectives**:
- Continuous optimization
- Advanced analytics and insights
- Strategic data value realization
- Innovation and experimentation

**Key Initiatives**:
1. **Continuous Optimization** (Month 19-20)
   - Implement continuous improvement process
   - Deploy optimization algorithms
   - Automate routine tasks
   - Reduce manual interventions

2. **Advanced Analytics** (Month 20-21)
   - Implement data value analytics
   - Deploy usage analytics
   - Create business impact metrics
   - Establish ROI tracking

3. **Strategic Alignment** (Month 21-22)
   - Align data governance with business strategy
   - Create data strategy roadmap
   - Establish data product management
   - Implement data monetization

4. **Innovation** (Month 22-23)
   - Experiment with new technologies
   - Pilot advanced capabilities
   - Share best practices
   - Build data culture

5. **Maturity Assessment** (Month 23-24)
   - Conduct comprehensive maturity assessment
   - Benchmark against industry
   - Identify improvement opportunities
   - Plan next maturity cycle

**Success Criteria**:
- Continuous optimization operational
- Business value metrics tracked
- Strategic alignment achieved
- Innovation culture established
- Level 5 maturity achieved

**Tools to Deploy**:
- Advanced analytics platforms
- AI/ML for governance automation
- Business intelligence tools

#### Roadmap Success Factors

**Critical Success Factors**:
1. **Executive Sponsorship**: Strong executive support throughout
2. **Change Management**: Effective change management and communication
3. **Resource Allocation**: Adequate resources (people, budget, tools)
4. **Training**: Comprehensive training and skill development
5. **Measurement**: Regular measurement and reporting
6. **Continuous Improvement**: Culture of continuous improvement

**Common Challenges**:
- Resistance to change
- Resource constraints
- Tool complexity
- Data quality issues
- Integration challenges

**Mitigation Strategies**:
- Start with quick wins
- Focus on high-value use cases
- Provide adequate training
- Choose appropriate tools
- Ensure strong sponsorship

### 11. Implementation Roadmap (Quick Reference)

#### Phase 1: Foundation (Months 1-3)
- Establish data governance council
- Define data governance policies
- Implement data classification framework
- Set up basic data catalog (OpenMetadata/DataHub)

#### Phase 2: Quality and Lineage (Months 4-6)
- Implement data quality framework (Great Expectations)
- Set up data lineage tracking (OpenLineage)
- Deploy quality monitoring tools
- Establish quality metrics and KPIs

#### Phase 3: Security and Compliance (Months 7-9)
- Implement data security controls (Immuta/Privacera)
- Set up privacy management
- Establish compliance monitoring
- Conduct compliance assessments

#### Phase 4: Optimization (Months 10-12)
- Optimize data processes
- Advanced analytics and insights
- Continuous improvement
- Maturity assessment and planning

## Tools and Technologies

### Data Catalog
- Apache Atlas
- Collibra
- Alation
- DataHub
- Amundsen

### Data Quality
- Great Expectations
- Apache Griffin
- Datafold
- Monte Carlo
- Soda

### Data Lineage
- OpenLineage
- Apache Atlas
- Manta
- MANTA
- DataKitchen

### Data Security
- HashiCorp Vault
- Immuta
- Privacera
- BigID
- OneTrust

## Best Practices

1. **Start with Business Value**: Focus on high-value use cases
2. **Executive Sponsorship**: Secure executive support
3. **Change Management**: Manage organizational change
4. **Technology Enablement**: Use appropriate tools
5. **Continuous Improvement**: Regular assessment and improvement
6. **Training and Awareness**: Build data literacy
7. **Metrics and KPIs**: Measure and report progress
8. **Collaboration**: Foster cross-functional collaboration

## Related Documents

- [MLOps Lifecycle](./mlops-lifecycle.md)
- [Security Best Practices](./security-best-practices.md)
- [Enterprise Architecture Maturity](./enterprise-architecture-maturity.md)
- [Data Warehouse Integration](./reference-architectures/data-warehouse-integration.md)

## References

- DAMA-DMBOK 2.0: Data Management Body of Knowledge
- COBIT 2019: Control Objectives for Information and Related Technologies
- ISO/IEC 38500: IT Governance
- DCAM: Data Management Capability Assessment Model
- CMMI: Capability Maturity Model Integration

