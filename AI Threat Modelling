# 🛡️ AI Threat Modelling for Enterprise Systems

## Overview

Artificial Intelligence systems are now deeply embedded in enterprise infrastructure, powering customer support, fraud detection, recommendations, and automated decision-making.

Unlike traditional applications, AI systems introduce new attack surfaces, non-deterministic behavior, and data-driven failure modes that require an evolved threat modelling approach.

This document presents a structured AI threat modelling methodology using:

- STRIDE adapted for AI systems
- MITRE ATLAS technique mapping
- OWASP LLM Top 10 architecture mapping

---

# 1. AI Security Assets

AI systems introduce asset classes that go beyond traditional databases and APIs.

## Key AI Assets

- **Training Data** — Data used to train models; poisoning leads to embedded malicious behavior.
- **Model Weights / Parameters** — The learned intelligence of the model; theft equals full model replication.
- **Embedding Vectors** — Used in RAG and recommendation systems; manipulation alters retrieval behavior.
- **System Prompts** — Hidden instructions defining model behavior; leakage exposes internal logic.
- **Feature Stores** — Real-time inputs feeding inference pipelines; tampering affects predictions.
- **Model Registry / Artifacts** — Versioned models for deployment; compromise enables model swapping attacks.

### Key Insight

Unlike traditional systems, AI assets directly encode behavior. Compromise is not just data loss — it is behavioral manipulation of the system itself.

---

# 2. AI System Characteristics Affecting Security

## Non-deterministic Behavior
AI models may produce different outputs for identical inputs, complicating:
- testing
- forensic analysis
- incident reproduction

## Black Box Nature
Deep learning models lack explainability at the code level, requiring:
- input/output analysis
- behavioral monitoring
- probabilistic threat modelling

---

# 3. AI Data Supply Chain

AI systems extend traditional software supply chain risks with data-driven dependencies.

## Lifecycle Stages

### 1. Data Collection
Sources include web scraping, third-party datasets, and user-generated content.
➡ Risk: malicious data injection at origin

### 2. Data Cleaning & Labeling
Manual or automated labeling introduces trust dependencies.
➡ Risk: mislabeled or poisoned training signals

### 3. Model Training
Poisoned data becomes embedded in model weights.
➡ Risk: persistent behavioral corruption requiring full retraining

### 4. Model Validation & Registry
Models are versioned and stored for deployment.
➡ Risk: registry tampering or backdoored model replacement

### 5. Inference
Model interacts with real-world inputs and retrieval systems.
➡ Risk: prompt injection and RAG manipulation

---

## Key Supply Chain Insight

AI supply chain attacks are **time-delayed**:

- Traditional: immediate compromise  
- AI: latent corruption discovered after deployment

---

# 4. STRIDE Adapted for AI Systems

## S — Spoofing (Data Source Impersonation)

Attackers manipulate trusted data sources in RAG pipelines or impersonate AI services.

**Example:**
Injected malicious documents in a knowledge base cause LLM responses to rely on false information.

---

## T — Tampering (Data Poisoning & Prompt Injection)

Manipulation of training data, model weights, or inference inputs.

Includes:
- training data poisoning
- feature manipulation
- prompt injection attacks
- model swapping in registries

**Example:**
Fraud dataset poisoning gradually shifts detection boundaries.

---

## R — Repudiation (Lack of AI Auditability)

AI systems often lack reproducible decision logs.

Issues:
- missing prompt/context history
- unclear model versions
- untraceable inference decisions

**Example:**
Unable to explain why a fraud transaction was approved weeks earlier.

---

## I — Information Disclosure (Model Extraction)

AI systems expose sensitive data through:

- model extraction attacks
- training data leakage
- system prompt disclosure
- embedding inversion

**Example:**
Competitor reconstructs recommendation logic via API queries.

---

## D — Denial of Service (Inference Cost Exploitation)

AI systems are vulnerable to resource-based attacks:

- token flooding
- expensive prompt exploitation
- GPU exhaustion
- “denial of wallet” attacks

**Example:**
Cost of inference spikes 10x due to malicious prompt floods.

---

## E — Elevation of Privilege (Jailbreaking & Tool Abuse)

Attackers bypass model restrictions or exploit tool access.

Includes:
- prompt jailbreaks
- excessive agent permissions
- tool chaining abuse

**Example:**
Jailbroken chatbot gains unauthorized database access.

---

# 5. STRIDE Limitations in AI Systems

STRIDE does not fully capture:

- adversarial examples (cross-category behavior)
- model bias and fairness issues
- emergent LLM capabilities
- probabilistic misalignment risks

These gaps require supplementary frameworks.

---

# 6. MITRE ATLAS Mapping

:contentReference[oaicite:0]{index=0} ATLAS provides a structured catalogue of AI/ML attack techniques.

## Key Techniques

- AML.T0020 — Data Poisoning (Tampering)
- AML.T0024 — Model Extraction (Information Disclosure)
- AML.T0015 — Evasion Attacks
- AML.T0051 — Prompt Injection
- AML.T0018 — Backdoor ML Model

---

## ATLAS Structure

- **Tactic** → attacker objective  
- **Technique** → method used  
- **Sub-technique** → variant implementation  
- **Mitigation** → defensive controls  

---

## Example Application

Data poisoning detected in training pipeline:

- STRIDE: Tampering  
- ATLAS: AML.T0020  
- Mitigations:
  - data provenance tracking
  - anomaly detection
  - model drift monitoring

---

# 7. OWASP LLM Top 10 Mapping

:contentReference[oaicite:1]{index=1} LLM Top 10 maps risks to system components.

## Key Component Risks

### LLM Inference Endpoint
- prompt injection
- sensitive data leakage
- jailbreak attacks
- unbounded consumption

### Vector Database / RAG Pipeline
- indirect prompt injection
- embedding manipulation
- misinformation injection

### Training Pipeline
- data poisoning
- supply chain compromise
- third-party dataset risks

---

## Example Mapping Insight

- Prompt Injection → LLM + RAG layer  
- Data Poisoning → training pipeline  
- Model Extraction → inference API layer  
- Excessive Agency → tool/agent layer  

---

# 8. Multi-Layer Threat Modelling Approach

Effective AI threat modelling requires combining three layers:

## 1. STRIDE-AI
Defines *what* can go wrong

## 2. MITRE ATLAS
Defines *how* it happens

## 3. OWASP LLM Top 10
Defines *where* it happens

---

## Final Insight

AI systems are not traditional applications with added intelligence.

They are **data-driven, probabilistic systems where data integrity = system integrity**.

Threat modelling must therefore evolve from:

> component-based security → behavior-based security
