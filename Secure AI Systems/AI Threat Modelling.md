# AI Threat Modelling for Enterprise AI Systems

Assess enterprise AI/ML deployments by identifying AI-specific assets, modelling threats, mapping attack techniques, and designing security controls using STRIDE, MITRE ATLAS, and the OWASP LLM Top 10 (2025).

---

## Overview

Artificial Intelligence systems introduce entirely new security challenges beyond those found in traditional software. Unlike conventional applications, AI systems rely on training data, model weights, embeddings, prompt engineering, and inference pipelines that significantly expand the attack surface.

In this project, I performed a structured security assessment of enterprise AI deployments by applying established threat modelling methodologies adapted specifically for AI systems.

Using a fictional enterprise environment (MegaCorp), I evaluated AI assets, analysed trust boundaries, mapped threats using STRIDE, enriched findings with MITRE ATLAS, and prioritised security risks using the OWASP LLM Top 10 (2025).

The objective was to understand how attackers target AI systems and how defenders can systematically identify, document, and mitigate those risks before deployment.

---

# Objectives

Throughout this project I learned to:

- Identify AI-specific assets within enterprise architectures
- Analyse AI attack surfaces and trust boundaries
- Understand the AI data supply chain
- Adapt STRIDE for AI threat modelling
- Use MITRE ATLAS to classify AI attack techniques
- Map risks using the OWASP LLM Top 10 (2025)
- Assess AI architectures from a defender's perspective
- Recommend security controls for AI deployments
- Prioritise AI security risks based on architectural exposure

---

# Project Scenario

MegaCorp has deployed several AI-powered services across its enterprise infrastructure:

- Customer Support Chatbot using a Large Language Model (LLM)
- Retrieval-Augmented Generation (RAG) knowledge base
- Internal Recommendation Engine
- Fraud Detection Machine Learning System

As part of the security team, I conducted a complete threat assessment before executive review, identifying security risks across every stage of the AI lifecycle.

---

# Skills Demonstrated

- AI Security Architecture Review
- Threat Modelling
- AI Risk Assessment
- Security Architecture Analysis
- STRIDE Threat Analysis
- MITRE ATLAS Mapping
- OWASP LLM Top 10 Assessment
- Secure Design Review
- Risk Prioritisation
- Defensive Security Documentation

---

# Technologies & Frameworks

## AI Security

- Large Language Models (LLMs)
- Retrieval-Augmented Generation (RAG)
- Embedding Models
- Vector Databases
- Machine Learning Pipelines

## Threat Modelling

- STRIDE
- MITRE ATLAS
- OWASP LLM Top 10 (2025)

## Security Principles

- Least Privilege
- Defence in Depth
- Secure by Design
- Shift-Left Security
- Zero Trust Concepts

---

# AI System Assets

Unlike traditional applications, AI systems introduce entirely new assets that require protection.

| Asset | Security Importance |
|----------|-------------------|
| Training Data | Determines model behaviour and can be poisoned |
| Model Weights | Represent the trained intelligence of the model |
| Embedding Vectors | Drive semantic search and RAG retrieval |
| System Prompts | Define model behaviour and security constraints |
| Feature Stores | Supply inference-time features |
| Model Registry | Stores production-ready AI models |

Understanding these assets is essential because compromising them can affect every future inference the model performs.

---

# AI Data Supply Chain

I analysed the complete lifecycle of an enterprise AI model.

## Stage 1 — Data Collection

- Internal datasets
- Public datasets
- User-generated content
- Third-party providers

### Risks

- Data poisoning
- Malicious contributions
- Untrusted data sources

---

## Stage 2 — Data Cleaning & Labelling

Activities included:

- Data preprocessing
- Filtering
- Annotation
- Label generation

### Risks

- Incorrect labels
- Malicious annotation
- Silent model bias

---

## Stage 3 — Model Training

The model learns from prepared datasets.

### Risks

- Poisoned training data
- Backdoored models
- Persistent behavioural manipulation

---

## Stage 4 — Validation & Model Registry

Models are validated before deployment.

### Risks

- Model replacement
- Registry compromise
- Backdoored production models

---

## Stage 5 — Inference

Users interact with deployed models.

### Risks

- Prompt Injection
- Sensitive Information Disclosure
- Model Extraction
- Excessive Tool Access

---

# AI Attack Surface Assessment

Compared to traditional applications, AI systems introduce significantly more attack surfaces.

| Traditional Application | AI-Augmented Application |
|--------------------------|--------------------------|
| API Endpoints | LLM Inference Endpoint |
| Database | Vector Database |
| Authentication | Prompt Construction |
| Business Logic | Orchestration Layer |
| Application Server | Tool Calling Framework |
| Logging | Conversation Memory |
| Configuration | System Prompts |
| External APIs | AI Plugins & Agents |

---

# AI-Specific Threat Modelling Using STRIDE

I adapted the traditional STRIDE framework for AI systems.

| STRIDE Category | AI Example |
|-----------------|------------|
| Spoofing | Impersonating trusted AI users |
| Tampering | Training Data Poisoning |
| Repudiation | Untracked AI decisions |
| Information Disclosure | Model Extraction |
| Denial of Service | Token exhaustion |
| Elevation of Privilege | Excessive AI Tool Access |

The assessment demonstrated that traditional STRIDE remains valuable but requires AI-specific interpretation.

---

# MITRE ATLAS Mapping

To enrich the threat model, I mapped identified risks to MITRE ATLAS techniques.

| Technique | Technique ID |
|------------|--------------|
| Data Poisoning | AML.T0020 |
| Model Extraction | AML.T0024 |
| Prompt Injection | AML.T0051 |
| Evade ML Model | AML.T0015 |
| Backdoor ML Model | AML.T0018 |

MITRE ATLAS provides technical detail beyond STRIDE by documenting attacker behaviours, techniques, and defensive mitigations.

---

# OWASP LLM Top 10 (2025) Assessment

The project mapped enterprise AI components against the OWASP LLM Top 10.

| Risk | Target Component |
|------|------------------|
| LLM01 Prompt Injection | Inference Endpoint / RAG |
| LLM02 Sensitive Information Disclosure | LLM / Training Pipeline |
| LLM03 Supply Chain | Training Pipeline |
| LLM04 Data & Model Poisoning | Model Registry |
| LLM05 Improper Output Handling | APIs / Frontend |
| LLM06 Excessive Agency | Tool Integrations |
| LLM07 System Prompt Leakage | Prompt Configuration |
| LLM08 Embedding Weaknesses | Vector Database |
| LLM09 Misinformation | LLM Responses |
| LLM10 Unbounded Consumption | API Gateway |

This mapping allowed architectural risks to be prioritised based on where they occur.

---

# Enterprise Threat Assessment

During the assessment I identified several high-priority risks affecting MegaCorp's AI deployment.

## Prompt Injection

Attackers manipulate prompts to alter model behaviour.

Mitigations:

- Prompt validation
- Context isolation
- Prompt boundary enforcement

---

## Data Poisoning

Malicious data corrupts model behaviour during training.

Mitigations:

- Dataset provenance
- Data validation
- Drift monitoring

---

## Model Extraction

Attackers reconstruct proprietary models through repeated API queries.

Mitigations:

- Query rate limiting
- Output restrictions
- API monitoring

---

## System Prompt Leakage

Hidden system instructions become exposed.

Mitigations:

- Never store secrets in prompts
- Secure prompt engineering
- Prompt isolation

---

## Excessive Agency

AI receives permissions beyond operational requirements.

Mitigations:

- Least Privilege
- Human Approval Workflows
- Scoped API Tokens

---

## Sensitive Information Disclosure

Models expose confidential enterprise information.

Mitigations:

- PII Redaction
- Encryption
- Output Filtering

---

## Unbounded Consumption

Abuse of inference endpoints causes excessive operational costs.

Mitigations:

- Rate Limiting
- Token Quotas
- Cost Monitoring

---

# Secure Design Principles Applied

Throughout the assessment, I recommended several security-by-design controls.

## Defence in Depth

Implemented controls across every trust boundary rather than relying on a single security layer.

## Least Privilege

Restricted model permissions to only the minimum required functionality.

## Input Validation

Validated prompts before model processing.

## Output Validation

Prevented AI-generated output from being trusted by downstream systems.

## Continuous Monitoring

Recommended monitoring of:

- Prompt activity
- Tool invocation
- Token consumption
- Model drift
- Cost anomalies
- Security events

---

# Key Takeaways

This project demonstrated that securing AI systems requires more than traditional application security.

Key lessons include:

- AI introduces entirely new assets requiring protection.
- AI systems have a dedicated data supply chain with unique attack vectors.
- STRIDE remains useful but must be adapted for AI.
- MITRE ATLAS provides a structured catalogue of AI attack techniques.
- The OWASP LLM Top 10 enables defenders to map risks directly to architectural components.
- Secure AI depends on least privilege, defence in depth, continuous monitoring, and secure design from the earliest stages of development.

---

# Learning Outcomes

By completing this project I gained practical experience in:

- AI threat modelling
- Enterprise AI risk assessment
- AI architecture analysis
- Defensive AI security
- Secure AI design
- AI governance frameworks
- OWASP LLM Top 10 (2025)
- MITRE ATLAS
- STRIDE adaptation for AI
- Enterprise security documentation

---

# References

- OWASP LLM Top 10 (2025)
- MITRE ATLAS
- STRIDE Threat Modelling
- AI Security Engineering Principles
- Enterprise AI Risk Management
