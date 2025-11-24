# Performance Benchmarks and SLAs for Enterprise LLM

Comprehensive performance benchmarks, SLA definitions, and monitoring guidelines for enterprise LLM deployments.

## TL;DR

Setting SLAs for LLM systems is tricky - performance varies wildly based on model size, hardware, and query complexity. This document gives you realistic benchmarks and SLA definitions based on what we've seen in production. Use these as starting points, then adjust based on your specific use case.

**Key Takeaways**:
- Interactive UI should be < 2s (P95) - users expect fast responses
- Critical APIs need < 1s (P95) - anything slower feels broken
- Availability targets depend on use case - 99.9% for most, 99.99% for critical
- Monitor everything - latency, throughput, quality, cost
- Benchmarks are guidelines, not gospel - your mileage will vary

## Overview

This document gives you realistic performance benchmarks and SLA definitions for enterprise LLM systems. We've covered latency, throughput, availability, accuracy, and cost metrics based on real-world deployments. Use these as starting points, then tune based on your specific models, hardware, and use cases.

## Performance Metrics

### 1. Latency Metrics

#### Response Time (P50, P95, P99)
Here's what these percentiles actually mean:
- **P50 (Median)**: Half your requests are faster, half are slower - this is your "typical" response time
- **P95**: 95% of requests are faster than this - this is what most users experience
- **P99**: 99% of requests are faster - this catches your worst-case scenarios
- **Max**: The absolute worst case - usually means something broke

**Why P95 matters more than P50**: Your P50 might be great, but if P95 is terrible, users will complain. Focus on P95.

#### Time to First Token (TTFT)
This is critical for streaming responses - users want to see something happening immediately:
- Time from request to first token generation
- For streaming, this is what users notice first
- **Target: < 200ms for P95** - anything slower feels laggy

#### Time per Output Token (TPOT)
How fast tokens stream after the first one:
- Average time to generate each token
- Affects how "smooth" streaming feels
- **Target: < 50ms per token for P95** - this gives you ~20 tokens/second, which feels natural

#### End-to-End Latency
The total time from when user clicks "send" to when they get the full response:
- Includes network, processing, and response time
- **Target: < 2s for P95 (interactive)** - users expect quick answers
- **Target: < 5s for P95 (batch)** - batch processing can be slower

### 2. Throughput Metrics

#### Requests Per Second (RPS)
- Number of requests processed per second
- Peak vs. sustained throughput
- Target: 100+ RPS per GPU (model-dependent)

#### Tokens Per Second (TPS)
- Number of tokens generated per second
- Model and hardware dependent
- Target: 50+ TPS per GPU (7B model)

#### Concurrent Users
- Number of simultaneous users supported
- Depends on infrastructure capacity
- Target: 100+ concurrent users

### 3. Availability Metrics

#### Uptime
- Percentage of time system is available
- Target: 99.9% (8.76 hours downtime/year)
- Target: 99.99% (52.56 minutes downtime/year)

#### Mean Time Between Failures (MTBF)
- Average time between system failures
- Target: > 720 hours (30 days)

#### Mean Time To Recovery (MTTR)
- Average time to recover from failure
- Target: < 15 minutes

### 4. Quality Metrics

#### Accuracy
- Task-specific accuracy metrics
- Classification accuracy, F1 score, etc.
- Target: > 90% for production use cases

#### Relevance
- Relevance of responses to queries
- Measured via human evaluation or automated metrics
- Target: > 85% relevance score

#### Coherence
- Logical consistency of responses
- Measured via evaluation frameworks
- Target: > 80% coherence score

#### Hallucination Rate
- Percentage of factually incorrect responses
- Critical for production systems
- Target: < 5% hallucination rate

### 5. Resource Utilization

#### GPU Utilization
- Percentage of GPU time used
- Target: > 70% average utilization

#### Memory Utilization
- Memory usage percentage
- Target: < 80% average utilization

#### CPU Utilization
- CPU usage percentage
- Target: < 70% average utilization

#### Network Utilization
- Network bandwidth usage
- Target: < 80% average utilization

### 6. Cost Metrics

#### Cost Per Request
- Total cost divided by number of requests
- Includes infrastructure and API costs
- Target: < $0.01 per request (use case dependent)

#### Cost Per Token
- Cost per generated token
- Model and infrastructure dependent
- Target: < $0.0001 per token

#### Infrastructure Cost
- Compute, storage, network costs
- Target: Optimize for workload patterns

## SLA Definitions

### Tier 1: Critical Services

**Availability**: 99.99% (52.56 minutes downtime/year)
**Latency**: P95 < 1s, P99 < 2s
**Throughput**: 100+ RPS sustained
**Accuracy**: > 95%
**MTTR**: < 10 minutes

**Use Cases**:
- Real-time customer support
- Critical business decisions
- Financial transactions
- Healthcare applications

### Tier 2: Standard Services

**Availability**: 99.9% (8.76 hours downtime/year)
**Latency**: P95 < 2s, P99 < 5s
**Throughput**: 50+ RPS sustained
**Accuracy**: > 90%
**MTTR**: < 30 minutes

**Use Cases**:
- Document search
- Content generation
- Data analysis
- General business applications

### Tier 3: Batch Services

**Availability**: 99.5% (43.8 hours downtime/year)
**Latency**: P95 < 30s, P99 < 60s
**Throughput**: 10+ RPS sustained
**Accuracy**: > 85%
**MTTR**: < 1 hour

**Use Cases**:
- Batch processing
- Report generation
- Data transformation
- Non-critical applications

## Performance Benchmarks by Use Case

### 1. Conversational AI / Chat

**Metrics**:
- Response Time: P95 < 1.5s
- TTFT: < 200ms
- TPOT: < 50ms
- Accuracy: > 90%
- Relevance: > 85%

**Benchmarks**:
- 7B model: 50+ TPS, 100+ concurrent users
- 13B model: 30+ TPS, 50+ concurrent users
- 70B model: 10+ TPS, 20+ concurrent users

### 2. Text-to-SQL / Data Analysis

**Metrics**:
- Response Time: P95 < 3s
- SQL Accuracy: > 85%
- Query Execution: < 5s
- Relevance: > 90%

**Benchmarks**:
- Query generation: < 1s
- Schema understanding: < 500ms
- Result formatting: < 500ms

### 3. Document Search / RAG

**Metrics**:
- Search Latency: P95 < 500ms
- Retrieval Accuracy: > 90%
- Response Generation: P95 < 2s
- Relevance: > 85%

**Benchmarks**:
- Vector search: < 100ms (1M vectors)
- Context assembly: < 200ms
- Response generation: < 1.5s

### 4. Code Generation

**Metrics**:
- Response Time: P95 < 5s
- Code Quality: > 80% (human evaluation)
- Compilation Success: > 70%
- Relevance: > 85%

**Benchmarks**:
- Code completion: < 500ms
- Function generation: < 3s
- Full file generation: < 10s

### 5. Content Generation

**Metrics**:
- Response Time: P95 < 10s
- Quality Score: > 80%
- Relevance: > 85%
- Coherence: > 80%

**Benchmarks**:
- Short content (< 500 words): < 5s
- Medium content (500-2000 words): < 15s
- Long content (> 2000 words): < 30s

## Infrastructure Benchmarks

### GPU Performance

**NVIDIA A100 (80GB)**:
- 7B model: 50-70 TPS
- 13B model: 30-40 TPS
- 70B model: 8-12 TPS

**NVIDIA H100 (80GB)**:
- 7B model: 80-100 TPS
- 13B model: 50-60 TPS
- 70B model: 15-20 TPS

**NVIDIA A10G (24GB)**:
- 7B model: 30-40 TPS
- 13B model: 15-20 TPS
- 70B model: Not supported (insufficient memory)

### Memory Requirements

**Model Sizes**:
- 7B model: ~14GB (FP16), ~7GB (INT8)
- 13B model: ~26GB (FP16), ~13GB (INT8)
- 70B model: ~140GB (FP16), ~70GB (INT8)

**Recommendations**:
- Reserve 20% memory for overhead
- Plan for batch processing
- Consider quantization for memory efficiency

## Monitoring and Alerting

### Key Metrics to Monitor

**Latency**:
- P50, P95, P99 response times
- TTFT and TPOT
- End-to-end latency

**Throughput**:
- RPS and TPS
- Concurrent requests
- Queue depth

**Availability**:
- Uptime percentage
- Error rates
- Failure frequency

**Quality**:
- Accuracy metrics
- Relevance scores
- Error types

**Resources**:
- GPU/CPU/Memory utilization
- Network bandwidth
- Storage usage

### Alerting Thresholds

**Critical Alerts**:
- Availability < 99%
- P95 latency > 2x target
- Error rate > 5%
- Resource utilization > 90%

**Warning Alerts**:
- Availability < 99.5%
- P95 latency > 1.5x target
- Error rate > 2%
- Resource utilization > 80%

### Dashboards

**Executive Dashboard**:
- Overall availability
- Total requests
- Average latency
- Cost metrics

**Operations Dashboard**:
- Real-time metrics
- Error rates
- Resource utilization
- Alert status

**Performance Dashboard**:
- Latency percentiles
- Throughput metrics
- Quality metrics
- Trend analysis

## Performance Optimization

### Model Optimization

**Quantization**:
- INT8 quantization: 2x speedup, minimal accuracy loss
- INT4 quantization: 4x speedup, some accuracy loss
- Dynamic quantization: Balance speed and accuracy

**Pruning**:
- Remove unnecessary parameters
- Reduce model size
- Maintain accuracy

**Distillation**:
- Train smaller models from larger models
- Reduce inference cost
- Maintain performance

### Infrastructure Optimization

**Caching**:
- Response caching for common queries
- Embedding caching
- Context caching

**Load Balancing**:
- Distribute load across instances
- Health-based routing
- Geographic distribution

**Auto-Scaling**:
- Scale based on demand
- Predictive scaling
- Cost optimization

### Pipeline Optimization

**Batch Processing**:
- Batch requests for efficiency
- Optimal batch sizes
- Batch scheduling

**Parallel Processing**:
- Parallel model inference
- Parallel data processing
- Pipeline parallelism

## Cost Optimization

### Infrastructure Costs

**Compute Optimization**:
- Right-size instances
- Use spot instances for batch
- Auto-scale based on demand

**Storage Optimization**:
- Tiered storage (hot/warm/cold)
- Data compression
- Lifecycle policies

**Network Optimization**:
- CDN for static content
- Data locality
- Bandwidth optimization

### Model Costs

**Model Selection**:
- Choose appropriate model size
- Use smaller models when possible
- Consider fine-tuning vs. base models

**Inference Optimization**:
- Batch processing
- Caching
- Quantization

## Benchmarking Methodology

### Test Environment

**Hardware**:
- Standardized test hardware
- Consistent configuration
- Isolated test environment

**Software**:
- Standardized software stack
- Version control
- Reproducible tests

### Test Scenarios

**Load Testing**:
- Gradual load increase
- Peak load testing
- Sustained load testing

**Stress Testing**:
- Beyond normal capacity
- Failure scenarios
- Recovery testing

**Endurance Testing**:
- Long-duration testing
- Memory leak detection
- Resource degradation

### Reporting

**Metrics Collection**:
- Automated metrics collection
- Statistical analysis
- Trend analysis

**Reporting**:
- Regular benchmark reports
- Comparison with baselines
- Performance trends

## Related Documents

- [On-Premise LLM Infrastructure](./reference-architectures/on-premise-llm-infrastructure.md)
- [MLOps Lifecycle](./mlops-lifecycle.md)
- [Cost Optimization](./cost-optimization.md)
- [Security Best Practices](./security-best-practices.md)

## References

- MLPerf Inference Benchmarks
- OpenAI API Performance
- Hugging Face Model Performance
- NVIDIA GPU Performance Guides

