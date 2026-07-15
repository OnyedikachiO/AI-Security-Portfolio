# SupplySecLab: AI Supply Chain Security Operations

> Advanced AI Security Engineering project demonstrating architecture inspection, dependency auditing, SBOM generation, API provider governance, and full incident response for AI supply chain attacks.

---

# Project Overview

After securing model serialization and integrity verification, the next phase of SupplySecLab focused on protecting every remaining layer of the AI supply chain.

Unlike traditional malware, AI threats can hide inside:

- Model architectures
- Dependency packages
- Third-party APIs
- Prompt templates
- Production deployment pipelines

This project demonstrates how these threats were identified, investigated, and prevented before production deployment.

---

# Objectives

- Detect malicious Keras Lambda layers
- Inspect model architecture without execution
- Audit Python dependencies
- Generate Software Bills of Materials (SBOM)
- Evaluate third-party AI providers
- Investigate AI supply chain incidents
- Assess production model candidates
- Build enterprise AI deployment governance

---

# Skills Demonstrated

- AI Security Engineering
- Secure ML Deployment
- Keras Security Analysis
- Dependency Auditing
- SBOM Generation
- Supply Chain Risk Assessment
- AI Incident Response
- Threat Hunting
- Model Governance
- AI Security Operations

---

# Technologies Used

- Linux
- Python
- Keras
- h5py
- ModelScan
- pip-audit
- Syft
- CycloneDX
- SPDX
- JSON
- API Security

---

# Project Architecture

```

Candidate Model

│

▼

Architecture Inspection

│

▼

Dependency Audit

│

▼

SBOM Generation

│

▼

Provider Assessment

│

▼

Sandbox Evaluation

│

▼

Production Decision

```

---

# Architecture-Level Threat Detection

## Background

Even when Pickle payloads are removed, attackers can still embed malicious code directly into a model's architecture.

Unlike serialization attacks, these payloads execute during inference rather than loading.

The objective was to inspect model architecture without executing it.

---

# Architecture Inspection

A clean image classifier contained:

```
InputLayer

↓

Flatten

↓

Dense

↓

Dense
```

Total Layers

```
4
```

---

A suspicious model contained:

```
InputLayer

↓

Flatten

↓

Dense

↓

Dense

↓

Lambda
```

Total Layers

```
5
```

---

## Evidence

### Clean Model Inspection

```bash
python3 inspect_h5_model.py image_classifier.h5
```

Evidence

```text
Total layers: 4

[OK] InputLayer

[OK] Flatten

[OK] Dense

[OK] Dense

RESULT

No suspicious layers detected
```

---

### Suspicious Model Inspection

```bash
python3 inspect_h5_model.py image_classifier_v2.h5
```

Evidence

```text
Total layers: 5

[WARNING]

Lambda

manipulate_output

Can contain arbitrary Python code
```

---

## Security Finding

Hidden Lambda layer detected:

```
manipulate_output
```

---

## Evidence Summary

✔ Architecture inspected safely

✔ Hidden Lambda layer identified

✔ Suspicious inference behavior detected

✔ Malicious model prevented from deployment

---

# Static Architecture Security Scanning

## Objective

Validate architecture findings using automated AI security tooling.

---

## Tool Used

ModelScan

---

### Evidence

```bash
modelscan image_classifier_v2.h5
```

Output

```text
Severity

MEDIUM

Unsafe operator

Lambda

Module

Keras
```

---

## Comparison

Clean Model

```text
No issues found
```

---

Suspicious Model

```text
Unsafe Lambda detected

Review required
```

---

## Security Outcome

Successfully differentiated legitimate neural network layers from potentially malicious custom logic.

---

## Evidence Summary

✔ Automated architecture scan

✔ Lambda detection confirmed

✔ Manual verification completed

---

# Dependency Auditing & SBOM Generation

## Background

Machine learning projects inherit risks from every package they install.

A single vulnerable dependency can compromise an otherwise secure model.

---

# Dependency Audit

Tool Used

```
pip-audit
```

---

## Evidence

### Requirements

```text
torch==2.1.0

transformers==4.35.0

numpy==1.24.3

Pillow==9.2.0

safetensors==0.4.0

accelerate==0.24.0
```

---

### Audit Execution

```bash
pip-audit requirements.txt
```

Evidence

```text
Multiple dependency vulnerabilities detected

Recommended fixed versions supplied
```

---

# SBOM Generation

Tool Used

```
Syft
```

---

### Evidence

```bash
syft project/ -o table
```

Output

```text
accelerate

numpy

pillow

safetensors

torch

transformers
```

---

Generated

```
CycloneDX SBOM
```

---

## Security Outcome

Created complete software inventory enabling:

- Vulnerability tracking
- Dependency visibility
- Compliance reporting
- Future patch management

---

## Evidence Summary

✔ Dependencies audited

✔ Vulnerabilities identified

✔ SBOM generated

✔ Supply chain visibility established

---

# Third-Party AI Provider Assessment

## Background

Unlike downloadable models, hosted LLM APIs cannot be scanned directly.

Instead, providers themselves must undergo security assessment.

---

## Assessment Areas

- Data handling
- Privacy
- Model versioning
- Security certifications
- Incident response
- Prompt governance
- Behavioral monitoring

---

## Behavioral Baseline

Established repeatable prompts for detecting silent provider updates.

```
Known Prompt

↓

Known Response

↓

Future Comparison

↓

Detect Drift
```

---

## Prompt Governance

Verified system prompts as controlled artifacts.

Compared:

### Internal Prompt

```
Verified

↓

Expected behavior

↓

Policy enforced
```

---

### External Prompt

```
Public template

↓

Wrong company

↓

Incorrect policies

↓

Missing guardrails
```

---

## Evidence

Provider incorrectly identified itself as

```
TryTrainML
```

instead of the organization's approved identity.

Confidentiality guardrails were also absent.

---

## Evidence Summary

✔ Behavioral baseline established

✔ Prompt governance validated

✔ Third-party provider assessed

✔ Unsafe provider configuration detected

---

# AI Supply Chain Incident Response

## Background

Investigated a simulated AI compromise affecting a production inference server.

The objective was to determine:

- Initial compromise
- Timeline
- Malicious payload
- Candidate replacement safety

---

# Investigation Workflow

```
Deployment Logs

↓

Timeline Analysis

↓

Production Model

↓

Payload Analysis

↓

Candidate Inspection

↓

Containment
```

---

## Evidence

### Deployment Timeline

Reviewed

```bash
deployment.log
```

Finding

```
Replacement model

Different organization

trustworthy-ai-lab
```

---

### Timeline

Deployment

↓

21 Days

↓

SOC Alert

---

### Payload Analysis

Decompiled production model.

Evidence

```python
system(
...
)
```

Shell execution identified using

```
os.system
```

---

### Network Investigation

Beacon Capture

Evidence

```
POST
```

Outbound HTTP request identified.

---

### Candidate Model Review

Executed

```bash
inspect_h5_model.py candidate_model.h5
```

Evidence

```text
Lambda Layer

manipulate_output
```

---

## Investigation Outcome

Successfully reconstructed:

- Initial compromise
- Deployment timeline
- Payload behavior
- Hidden architecture attack

before approving replacement deployment.

---

## Evidence Summary

✔ Deployment timeline reconstructed

✔ Payload analyzed

✔ Network beacon investigated

✔ Replacement model reviewed

✔ Hidden architecture backdoor identified

---

# Production Candidate Security Assessment

## Background

Four candidate AI review models were evaluated before production deployment.

Each underwent:

- Load inspection
- Telemetry analysis
- Guardrail verification
- Prompt governance review

---

# Candidate Assessment

| Candidate | Finding | Decision |
|------------|---------|----------|
| Candidate A | Attempted `/etc/passwd` access, disabled security review | ❌ Reject |
| Candidate B | SafeTensors, verified telemetry, guardrails enabled | ✅ Approve |
| Candidate C | Lambda layer executed custom code | ❌ Reject |
| Candidate D | Unverified API provider, unknown provenance | ❌ Reject |

---

## Evidence

### Candidate A

Telemetry

```text
Attempted file access

/etc/passwd
```

Guardrail

```text
security_review_flag

Disabled
```

Policy

```
CommunityReview
```

---

### Candidate B

Evidence

```text
SafeTensors

Verified

Internal Prompt

Guardrails Enabled

Needs Changes
```

No suspicious activity detected.

---

### Candidate C

Evidence

```text
Lambda Layer

exec(open())

Custom code

Blocked
```

---

### Candidate D

Evidence

```text
Unknown provenance

Vendor managed prompts

Compliance absent
```

---

## Final Recommendation

Production Approval

```
Candidate B
```

Production Rejection

```
Candidate A

Candidate C

Candidate D
```

---

# Key Security Concepts Demonstrated

- AI Model Governance
- Secure AI Deployment
- AI Architecture Inspection
- Lambda Layer Detection
- Dependency Auditing
- SBOM Generation
- API Security Assessment
- Behavioral Monitoring
- AI Incident Response
- Production Risk Assessment
- Supply Chain Governance
- Enterprise AI Security

---

# Project Outcome

Successfully completed the second phase of **SupplySecLab**, implementing enterprise-grade AI security controls across model architecture inspection, dependency management, SBOM generation, API provider governance, incident response, and production model approval.

The completed security pipeline demonstrates how organizations can systematically prevent compromised AI models, malicious dependencies, unsafe APIs, and architecture-level backdoors from reaching production environments through layered security validation and governance.

---

## Disclaimer

This project was completed in an **authorized cybersecurity training environment** designed to simulate real-world AI supply chain attacks against machine learning systems.

All activities were performed in an isolated laboratory environment.
