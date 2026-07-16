# Securing Retrieval-Augmented Generation (RAG) Systems Against Data Poisoning and Retrieval Abuse

> Designed and evaluated security controls for Retrieval-Augmented Generation (RAG) applications by analyzing retrieval pipelines, inference-time data poisoning, indirect prompt injection, context manipulation, and layered defensive architectures using modern AI security frameworks.

---

# Project Overview

Retrieval-Augmented Generation (RAG) has become one of the most widely adopted architectures for enterprise AI assistants because it allows Large Language Models (LLMs) to retrieve external knowledge during inference instead of relying solely on pre-trained parameters.

While RAG significantly improves response accuracy and freshness, it fundamentally changes the trust model of AI systems.

Unlike traditional LLMs, retrieved documents directly influence model reasoning without being verified by the model itself, creating entirely new attack surfaces such as:

- Inference-time data poisoning
- Retrieval abuse
- Indirect prompt injection
- Context manipulation
- Trust boundary failures
- Knowledge base poisoning

In this project, I analyzed the complete RAG architecture, identified security risks throughout the retrieval pipeline, studied real-world incidents, and designed layered defensive controls for securing enterprise RAG deployments.

---

# Objectives

- Understand enterprise RAG architecture
- Analyze inference-time trust boundaries
- Identify RAG-specific attack surfaces
- Demonstrate retrieval abuse techniques
- Study real-world retrieval failures
- Design layered security controls
- Map findings to OWASP, NIST AI RMF, and EU AI Act

---

# Skills Demonstrated

- AI Security Engineering
- RAG Architecture Analysis
- Threat Modeling
- Retrieval Pipeline Security
- Data Poisoning Analysis
- Prompt Injection Defense
- AI Governance
- Risk Assessment
- Defensive Architecture Design
- AI Security Framework Mapping

---

# Technologies & Concepts

- Retrieval-Augmented Generation (RAG)
- Vector Databases
- Embedding Models
- Semantic Search
- Context Windows
- Similarity Search
- Knowledge Bases
- Retrieval Pipelines
- Enterprise AI Assistants
- AI Trust Boundaries

---

# RAG Architecture Analysis

The project analyzed how enterprise RAG systems process user requests through multiple components before generating responses.

```
User Query
      │
      ▼
Embedding Model
      │
      ▼
Vector Database
      │
      ▼
Semantic Retrieval
      │
      ▼
Retrieved Documents
      │
      ▼
Context Injection
      │
      ▼
Large Language Model
      │
      ▼
Generated Response
```

Unlike traditional language models, retrieved content directly becomes part of the prompt context without independent verification.

This creates inference-time security risks that cannot be solved through model training alone.

---

# Security Assessment of the RAG Pipeline

The project evaluated security risks across every stage of the retrieval pipeline.

## Document Ingestion

Potential Risks

- Malicious document uploads
- Insider document manipulation
- Untrusted external knowledge sources
- Poisoned enterprise documentation
- Lack of ownership validation

Security Controls

- Source validation
- Approval workflows
- Document ownership tracking
- Version control
- Content review

---

## Embedding Generation

Potential Risks

- Loss of metadata
- Loss of document ownership
- Reduced security visibility
- Hidden malicious intent

Security Controls

- Metadata preservation
- Provenance tracking
- Embedding audits
- Document classification

---

## Similarity-Based Retrieval

Potential Risks

- Semantic retrieval abuse
- Retrieval poisoning
- Manipulated document ranking
- Retrieval of outdated information

Security Controls

- Trust-aware retrieval
- Freshness validation
- Confidence scoring
- Source reputation analysis

---

## Context Injection

Potential Risks

- Indirect prompt injection
- Hidden instructions
- Context manipulation
- Instruction overriding

Security Controls

- Prompt isolation
- Instruction filtering
- Context segmentation
- Guardrail enforcement

---

# RAG-Specific Attack Surface

The project identified several attack surfaces unique to Retrieval-Augmented Generation.

## Inference-Time Data Poisoning

Rather than retraining the model, attackers poison documents stored inside the knowledge base.

When retrieval occurs, the malicious documents become trusted context.

---

## Retrieval Abuse

Attackers manipulate semantic similarity so malicious documents rank highly during retrieval.

This allows harmful content to influence responses without modifying the prompt itself.

---

## Passive Poisoning

Malicious documents are uploaded once.

Future user queries automatically retrieve them.

Characteristics:

- Persistent
- Difficult to detect
- No continued attacker interaction

---

## Active Retrieval Manipulation

Attackers intentionally craft documents to maximize semantic similarity.

Goals include:

- Dominating search rankings
- Influencing sensitive queries
- Manipulating generated responses

---

## Context Manipulation

Retrieved documents may include:

- Hidden prompts
- Fake documentation
- Embedded instructions
- Misleading recommendations

Since the LLM cannot distinguish instructions from data, these become authoritative context.

---

# Threat Modeling

The project modeled trust boundaries within the retrieval pipeline.

```
External Documents
        │
        ▼
Document Ingestion
        │
        ▼
Embedding Generation
        │
        ▼
Vector Store
        │
        ▼
Retriever
        │
        ▼
Context Injection
        │
        ▼
LLM
        │
        ▼
User Response
```

Each transition represents a trust boundary requiring independent validation.

---

# Real-World Security Case Studies

## Microsoft 365 Copilot (2026)

Analysis focused on email-based retrieval abuse.

Key Findings

- Enterprise emails became retrieval sources
- Embedded instructions influenced responses
- Sensitive information exposure occurred
- Trust was incorrectly assigned during ingestion

Lessons Learned

- Internal data cannot automatically be trusted
- Retrieval must be treated as a security boundary

---

## ChatGPT Plugins

The project examined indirect prompt injection through external plugins.

Observed Issues

- External websites supplied prompt-like instructions
- Retrieved content bypassed traditional prompt protections
- Unsafe outputs occurred despite unchanged system prompts

Lessons Learned

- Retrieved content requires validation
- Data must remain separated from executable instructions

---

## Web-Connected AI Assistants

The project analyzed stale retrieval attacks.

Observed Issues

- Outdated documents remained indexed
- Semantic relevance outweighed freshness
- Users received obsolete guidance

Lessons Learned

- Retrieval pipelines require lifecycle management
- Freshness validation is essential

---

# Layered Defensive Architecture

The project designed a defense-in-depth strategy for securing enterprise RAG deployments.

## Layer 1

Secure Document Ingestion

- Source validation
- Content approval
- Ownership verification

---

## Layer 2

Embedding Security

- Metadata preservation
- Provenance tracking
- Document labeling

---

## Layer 3

Retrieval Protection

- Trust-aware ranking
- Freshness validation
- Confidence scoring

---

## Layer 4

Context Guardrails

- Prompt isolation
- Instruction detection
- Context segmentation

---

## Layer 5

Behavioral Monitoring

Continuous monitoring for:

- Output drift
- Retrieval anomalies
- Repeated document retrieval
- Abnormal response patterns

---

# Security Framework Alignment

## OWASP Top 10 for LLM Applications

- LLM01 – Indirect Prompt Injection
- LLM04 – Data & Model Poisoning
- LLM07 – Insecure Model Monitoring

---

## NIST AI Risk Management Framework

Applied principles from:

- MAP
- MEASURE
- MANAGE

to evaluate retrieval dependencies, document trust, monitoring, and governance.

---

## EU AI Act

Mapped security controls against:

- Article 9 – Risk Management
- Article 10 – Data Governance

---

# Key Security Takeaways

- Retrieval fundamentally changes AI trust boundaries.
- Inference-time attacks can manipulate outputs without retraining the model.
- Semantic relevance is not equivalent to trust.
- Retrieval abuse often appears as normal system behavior.
- Data poisoning can persist long after ingestion.
- Defense requires layered controls across ingestion, retrieval, context injection, and monitoring.

---

# Outcome

This project strengthened my understanding of securing Retrieval-Augmented Generation systems by evaluating retrieval pipelines, modeling AI trust boundaries, analyzing inference-time attacks, studying real-world security incidents, and designing layered defensive architectures aligned with OWASP LLM Top 10, NIST AI RMF, and the EU AI Act.

---

## Disclaimer

This project was completed in an authorized AI security training environment for educational and defensive purposes only. All demonstrations, attack scenarios, and mitigation techniques were performed against controlled lab systems and publicly documented case studies.
