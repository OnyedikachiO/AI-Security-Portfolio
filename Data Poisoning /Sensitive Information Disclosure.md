# Sensitive Information Disclosure

## Introduction

Large Language Models (LLMs) generate responses based on patterns learned during training and on data retrieved at runtime. Unlike traditional databases, they do not enforce strict row-level access rules by default. This creates a new class of confidentiality risk known as **Sensitive Information Disclosure**.

In LLM systems, disclosure typically does not occur because the model intentionally reveals confidential information. Instead, it happens because sensitive data has already entered the model's context window through the retrieval process.

Modern AI applications commonly implement **Retrieval-Augmented Generation (RAG)**, where relevant documents are retrieved from external knowledge sources before being supplied to the model. While this significantly improves answer quality, it also introduces additional opportunities for confidential information to be exposed if retrieval boundaries are not properly enforced.

This project explores how sensitive information disclosure occurs within retrieval pipelines, embedding systems, vector databases, and logging mechanisms. It also examines defensive strategies for preventing confidential information from being exposed through AI-generated responses.

---

## Learning Objectives

By completing this project, the following concepts were explored:

- Define Sensitive Information Disclosure (OWASP LLM02)
- Distinguish retrieval-based leakage from parametric memory
- Identify architectural points where sensitive information can leak
- Understand why confidentiality in LLM systems is primarily a system design problem
- Analyze real-world disclosure scenarios affecting Retrieval-Augmented Generation (RAG) systems

---

## OWASP LLM02 – Sensitive Information Disclosure

Sensitive Information Disclosure is categorized under **OWASP LLM02**.

This category focuses on protecting:

- Private information
- Proprietary business logic
- Confidential organizational documents
- Sensitive operational data

Unlike many traditional security vulnerabilities, disclosure in LLM applications is usually caused by weaknesses in the surrounding architecture rather than by the language model itself.

---

## Disclosure Is Different From Poisoning and Prompt Injection

Although these attacks target AI systems, they affect different parts of the architecture.

| Attack | Description |
|---------|-------------|
| Data Poisoning | Manipulates what the system learns or retrieves. |
| Prompt Injection | Manipulates instructions supplied to the model during inference. |
| Sensitive Information Disclosure | Exposes confidential information that already exists within the system. |

Sensitive Information Disclosure does not require modifying the model or changing stored data. Instead, attackers exploit weaknesses in retrieval systems, logging mechanisms, embeddings, or access controls to obtain confidential information.

---

## How Data Leakage Happens in Real Systems

Modern LLM applications frequently rely on **Retrieval-Augmented Generation (RAG)**.

Rather than depending solely on training data, RAG retrieves documents from a vector database and injects them into the model's context window before generating a response.

This architecture introduces several potential disclosure points:

- Overly broad similarity searches retrieving confidential documents
- Shared vector databases across multiple departments or tenants
- Large context windows containing unrelated document chunks
- Debug logging that stores complete prompts and retrieved documents
- Weak or missing metadata filtering

If unauthorized content is retrieved, the exposure already exists before the model produces an answer. The underlying weakness is architectural rather than conversational.

---

## Scenario: Exposure Without Breaking Anything

A cloud infrastructure provider deploys an AI-powered support assistant to help customers troubleshoot deployment issues, billing problems, and configuration errors. The assistant is built using a Retrieval-Augmented Generation (RAG) architecture and retrieves information from multiple knowledge sources, including:

- Public documentation
- Internal troubleshooting playbooks
- Archived support tickets
- Incident postmortem reports

To improve response quality, historical support tickets are embedded into the vector database. Many of these tickets contain sensitive information such as:

- Customer account IDs
- Deployment architecture diagrams
- API keys accidentally shared during troubleshooting
- Root cause analysis summaries

The engineering team assumes the information is safe because the tickets are internal, the model is stateless, and the system prompt instructs the model not to reveal confidential information.

A customer submits the following request:

> *"Why does my Kubernetes deployment keep failing when autoscaling triggers?"*

The retrieval system performs a similarity search across every embedded support ticket. A ticket belonging to another customer is ranked highly because it discusses a nearly identical autoscaling issue.

The retrieved document contains:

- Customer account identifiers
- Internal infrastructure naming conventions
- Configuration examples

Since the retrieval system lacks tenant isolation, the document is inserted directly into the model's context window.

While generating its response, the model includes a configuration example copied from the retrieved ticket containing the internal cluster identifier:

> `cluster-prod-eu-west-42`

Although no authentication mechanisms failed and no database permissions were bypassed, confidential infrastructure information belonging to another customer was exposed.

During a later compliance audit, security personnel discover an additional issue. Every augmented prompt, including the retrieved support ticket contents, has been stored within the organization's observability platform.

The disclosure did not occur because of the language model itself. Instead, it resulted from weaknesses within the retrieval pipeline and logging architecture, both of which prioritized relevance over isolation.

---

## Why Sensitive Information Disclosure Is Dangerous

Sensitive Information Disclosure often appears to be completely normal system behavior.

The AI responds successfully.

The application continues operating normally.

No alerts are generated.

No visible errors occur.

This makes confidentiality failures particularly difficult to detect.

Several architectural characteristics contribute to this risk:

- Vector databases retain embedded documents until they are explicitly removed.
- Embeddings organize semantically similar information close together within high-dimensional vector space.
- Logging platforms frequently have broader access permissions than the original data source.
- Access restrictions are sometimes implemented only within prompts rather than being enforced at the database layer.

When retrieval is not deterministic and authorization-aware, confidential information can silently cross organizational trust boundaries without triggering obvious indicators of compromise.

---

## Common Disclosure Scenario

Sensitive Information Disclosure rarely begins with an attacker exploiting a sophisticated vulnerability.

Instead, it usually follows the normal operation of a Retrieval-Augmented Generation pipeline:

1. The retrieval layer searches for relevant documents.
2. Retrieved content is inserted into an augmented prompt.
3. The augmented prompt is submitted to the language model.
4. The model generates a response using every token present within the context window.

The vulnerability originates when retrieval and logging components assume retrieved content is automatically authorized before verifying user permissions.

As a result, confidential information can be processed or stored even though no security controls were explicitly bypassed.

---

## Scenario: Multi-Tenant Support Platform

A technology company deploys an internal AI assistant to help engineers troubleshoot production incidents using Retrieval-Augmented Generation.

For every request, the assistant performs the following workflow:

1. Retrieves the most relevant internal documents.
2. Builds an augmented prompt containing:
   - User query
   - Retrieved document chunks
   - System instructions
3. Sends the prompt to the language model.
4. Logs the entire request for troubleshooting and quality monitoring.

To assist with hallucination analysis, verbose logging is enabled in production.

Each request records:

- User queries
- Retrieved document chunks
- Complete augmented prompts
- Final model responses

A senior engineer submits the following request:

> *"Summarise last quarter's authentication failure trends."*

The retrieval pipeline correctly retrieves:

- Public reliability metrics
- Internal security incident reports
- Confidential breach analysis documentation

Because the engineer is authorized, the generated summary is accurate and no immediate issues appear.

Later, during a routine security audit, investigators discover that every augmented prompt has been stored in plaintext within the centralized logging platform.

Although retrieval permissions were enforced correctly, the logging infrastructure grants access to DevOps personnel, support analysts, and external contractors who are not authorized to view confidential security reports.

The disclosure did not originate from the language model or the retrieval system.

Instead, the observability platform became an unintended secondary repository for sensitive organizational data.

---

## What Actually Happened

Within a Retrieval-Augmented Generation system, every augmented prompt typically contains:

- User input
- Retrieved documents
- System instructions

If these prompts are stored without redaction, the logging platform effectively becomes another sensitive data repository.

Logging environments frequently introduce additional confidentiality risks because they often provide:

- Broader access permissions
- Longer retention periods
- Less granular authorization controls

In this scenario, the language model did not expose confidential information through its generated response.

Instead, the infrastructure leaked sensitive information by storing complete prompts containing confidential retrieved documents.
```


---

## Case Study: EchoLeak (CVE-2025-32711)

The **EchoLeak** vulnerability demonstrated how sensitive information disclosure can occur when Retrieval-Augmented Generation (RAG) systems trust retrieved content without validating its source.

In this case, a malicious email containing hidden HTML instructions was delivered to a target user. Although the instructions were invisible to the recipient, they were parsed, indexed, and embedded into the system's knowledge base.

Later, when the user submitted a legitimate request such as:

> *"Summarise my unread emails."*

the retrieval layer selected the malicious email because its embedding was semantically similar to the query.

The retrieved content was inserted directly into the model's context window alongside trusted information.

Because the system failed to distinguish between instructions and retrieved data, the hidden content was interpreted as legitimate input. The embedded instructions directed the system to:

- Search for sensitive files
- Extract confidential information
- Exfiltrate retrieved data through a remote image URL

The disclosure occurred because:

- Retrieved content was inserted into the prompt without source validation.
- No mechanism separated trusted instructions from retrieved documents.
- Tool access was granted based on retrieved content rather than verified authorization.

This vulnerability demonstrated that RAG systems can transform stored content into an exfiltration channel when retrieval boundaries are not properly enforced.

---

## Case Study: Model Extraction via Output Probing (CVE-2019-20634 – "Proof Pudding")

The **Proof Pudding** attack demonstrated that sensitive information disclosure can occur without exposing raw documents.

Researchers targeted an email protection system that assigned spam probability scores to incoming messages. Although the model never revealed its training data, it consistently returned confidence scores for each prediction.

By submitting carefully crafted inputs and observing changes in these scores, researchers were able to:

- Infer how the model classified messages
- Approximate its internal decision logic
- Construct a surrogate model that closely replicated the original system

This demonstrated that exposing confidence scores, ranking information, latency differences, or retrieval behavior can allow attackers to reconstruct protected model behavior.

Within LLM systems, similar techniques may be used to:

- Approximate internal decision-making processes
- Infer characteristics of protected datasets
- Map behavioral boundaries of hidden models

The system did not disclose plaintext documents.

Instead, sensitive information was inferred through observable outputs.

Inference alone can therefore constitute a confidentiality breach.

---

# Attacks on Vector Databases

Modern Retrieval-Augmented Generation systems rely on embeddings to retrieve relevant documents.

An embedding is a numerical representation of text that captures semantic meaning by positioning similar content close together within a high-dimensional vector space.

When users submit a query, the system converts the query into an embedding and retrieves the closest stored vectors.

This retrieval process is driven entirely by mathematical similarity rather than authorization policies.

The model does not determine which documents are authorized.

Instead, the retrieval layer selects the highest-ranked vectors and inserts their corresponding text into the context window.

This is where disclosure risk begins.

---

## Similarity Search Controls Exposure

Similarity search determines which documents enter the model's context window.

Common similarity metrics include:

- Cosine Similarity
- Euclidean Distance

The vector database returns the **top-k** most similar embeddings.

Even small changes in wording can alter vector positions and change which documents appear among the highest-ranked results.

As a consequence:

- Similarity determines which documents become visible.
- Visibility determines what influences the model's response.

If confidential documents are mathematically closer to the query than public documents, they may be retrieved regardless of whether the requesting user is authorized to access them.

---

## Corpus Manipulation and Retrieval Influence

Vector databases store embeddings persistently.

Any newly inserted document becomes part of the searchable corpus.

If an embedding is positioned close to frequently issued queries, it may repeatedly appear within retrieval results.

Although the language model itself is never retrained, its responses change because the retrieval context has changed.

Manipulating retrieval behavior therefore directly influences what information enters the model's context window and may alter confidentiality boundaries.

---

## Embedding Inversion

Embeddings are often assumed to be anonymous because they consist of numerical vectors.

This assumption is incorrect.

Embedding inversion techniques attempt to reconstruct the approximate original text represented by stored vectors.

If attackers obtain access to a vector database, surrogate models may be trained to recover information such as:

- Named entities
- Project identifiers
- Numerical values
- Sensitive document themes

Although reconstruction may be incomplete, embeddings can still reveal meaningful information about protected documents.

The absence of plaintext does not eliminate disclosure risk.

---

## Membership Inference

Disclosure does not always require reconstructing stored documents.

Membership inference attacks attempt to determine whether specific records exist within a dataset.

Attackers generate candidate embeddings and observe similarity scores or system behavior to determine whether particular information is present.

In regulated environments, simply confirming that a person, project, or incident exists within a protected dataset may itself constitute a privacy breach.

Disclosure therefore includes both direct exposure and inference-based confirmation.

---

## Multi-Tenant Vector Stores

Many organizations store embeddings for multiple departments or customers within a shared vector database.

While this reduces operational complexity, it increases disclosure risk.

If metadata filtering is not applied before similarity search, documents belonging to different tenants may appear within the same retrieval results.

Prompt instructions such as:

> *"Only answer using documents belonging to User A."*

cannot prevent unauthorized documents from entering the context window.

Once retrieved, those documents have already crossed the intended security boundary.

Effective tenant isolation must therefore be enforced during retrieval rather than during response generation.

---

## Case Study: Milvus Proxy Authentication Bypass (CVE-2025-64513)

Milvus is a widely used open-source vector database designed to store and query high-dimensional embeddings for Retrieval-Augmented Generation (RAG) pipelines and semantic search systems.

In 2025, a critical authentication vulnerability (**CVE-2025-64513**) was identified within the Milvus Proxy component. The Proxy is responsible for authenticating and authorizing requests before they reach the vector database.

The vulnerability resulted from improper authentication logic. The Proxy trusted a user-controlled HTTP header (`sourceId`) to determine whether authentication had already occurred.

By forging this header, an attacker could bypass authentication without valid credentials.

Once exploited, an attacker could:

- Read stored vector embeddings
- Access associated plaintext metadata
- Modify existing embeddings
- Delete database collections

Although embeddings contain numerical values rather than plaintext, they preserve semantic relationships between documents.

Consequently, unauthorized access to embeddings alone constitutes a confidentiality risk because sensitive information can potentially be inferred or reconstructed.

This vulnerability demonstrates that vector databases should be treated as sensitive data stores rather than simple search infrastructure.

---

# RAG Retrieval Failure

Modern Large Language Models frequently rely on Retrieval-Augmented Generation (RAG) to supplement responses with external knowledge.

When users submit a query, the retrieval pipeline converts the request into an embedding, searches the vector database, retrieves the most relevant document chunks, and injects those documents into the model's context window before response generation.

If unauthorized or unrelated documents are retrieved, they become part of the model's input.

The exposure occurs before the language model generates any output.

Retrieval therefore represents a security boundary rather than merely a performance optimization.

---

## How Retrieval Controls Exposure

Retrieval follows a straightforward process:

1. The user's query is converted into an embedding.
2. A similarity search identifies the closest document vectors.
3. The highest-ranked document chunks are retrieved.
4. Retrieved content is inserted into the model's context window.

This ranking process is entirely mathematical.

Similarity search does not understand:

- User identity
- Authorization
- Department boundaries
- Tenant ownership

If metadata filtering is absent or incorrectly implemented before retrieval, confidential documents may enter the context window regardless of the requesting user's permissions.

Once retrieved, the model processes every token equally without distinguishing between authorized and unauthorized content.

---

## Over-Broad Top-k Retrieval

Increasing the **top-k** retrieval value generally improves answer quality by providing additional contextual information.

However, retrieving more documents also expands the disclosure surface.

A larger retrieval window may include:

- Loosely related confidential documents
- Historical records
- Sensitive internal reports
- Information outside the user's authorization scope

Because the model processes every retrieved token, increasing top-k increases the likelihood that confidential information enters the context window.

The retrieval ranking directly determines what information becomes visible to the model.

---

## Weak or Missing Metadata Filtering

Metadata filtering determines which documents are eligible for retrieval.

Common metadata includes:

- Tenant ID
- Department
- User role
- Classification level
- Access permissions

Filtering must occur **before** similarity search.

Applying filters after retrieval does not prevent confidential documents from influencing ranking or entering the retrieval pipeline.

Prompt instructions such as:

> *"Only answer using authorized documents."*

cannot replace database-level authorization.

Effective access control must be enforced during retrieval rather than delegated to the language model.

---

## Stale and Persistent Embeddings

Vector databases persist embeddings independently of their original documents.

If source documents are deleted or updated while their embeddings remain indexed, outdated information can continue to appear during retrieval.

This creates several risks:

- Deleted documents remain searchable.
- Obsolete information continues influencing responses.
- Compliance requirements may be violated.
- Sensitive historical data remains retrievable after removal from primary systems.

Retrieval pipelines must remain synchronized with their source-of-truth repositories to ensure obsolete embeddings are removed when underlying documents are deleted.

---

## Retrieval Misconfiguration Demonstration

A simulated Retrieval-Augmented Generation pipeline demonstrates how retrieval configuration directly influences confidentiality.

Three scenarios illustrate common retrieval behaviors:

### Scenario 1 — No Metadata Filtering (Top-k = 2)

The retrieval pipeline returns:

- Public policy documentation
- Confidential payroll documentation

The confidential document is selected because its embedding is nearly identical to the query embedding.

Similarity—not authorization—determines retrieval.

---

### Scenario 2 — No Metadata Filtering (Top-k = 3)

Increasing the retrieval limit introduces an additional document.

The retrieved results now include:

- Public documentation
- Confidential payroll information
- Deleted policy documentation

This demonstrates how increasing top-k expands the exposure surface by introducing confidential and obsolete information into the context window.

---

### Scenario 3 — Metadata Filtering Enabled

Metadata filtering is applied before similarity search.

Only documents satisfying authorization and deletion policies are eligible for ranking.

As a result:

- Confidential payroll records are excluded.
- Deleted documents are excluded.
- Only authorized public information is retrieved.

This demonstrates that deterministic filtering prevents unauthorized information from entering the model's context window.

---

## Case Study: Milvus Proxy Authentication Bypass (CVE-2025-64513)

The Milvus Proxy serves as the authentication gateway for vector database queries.

Its responsibility is to validate authentication before allowing similarity searches to execute.

The authentication flaw allowed attackers to bypass these checks by supplying a forged `sourceId` header.

As a result, attackers could:

- Execute arbitrary similarity searches
- Enumerate collections
- Retrieve stored embeddings
- Access associated metadata

The vector database continued operating normally because it had no knowledge that authentication had failed upstream.

This case demonstrates that retrieval gateways must enforce authentication before similarity search begins.

---

## Why Retrieval Is a Security Boundary

Retrieval determines which documents become part of the model's context window.

The language model does not receive information about:

- User identity
- Access permissions
- Document ownership
- Authorization policies

It simply processes every token supplied within the prompt.

Weak retrieval boundaries can therefore expose:

- Cross-tenant information
- Cross-department documents
- Archived records
- Deleted content
- Confidential metadata

Access control must be enforced before similarity search occurs.

Once unauthorized content enters the context window, the confidentiality boundary has already been crossed.
```

---

# Access Control and Data Segmentation

## Why Segmentation Matters in RAG Systems

Retrieval-Augmented Generation (RAG) systems retrieve documents based on similarity rather than identity. A shared vector store ranks documents according to semantic proximity without inherently distinguishing between users, departments, or tenants.

Without proper segmentation, queries may retrieve documents belonging to other users or organizations simply because they are mathematically similar.

Segmentation introduces policy enforcement into the retrieval process by restricting which documents are eligible for search before similarity ranking begins.

Common segmentation mechanisms include:

- Namespace filtering
- Per-tenant vector stores
- Metadata filtering
- Pre-query authorization checks

Without these controls, confidential information can silently cross organizational boundaries without triggering alerts or system errors.

---

## Deterministic Access Control

Traditional databases enforce authorization using mechanisms such as Access Control Lists (ACLs) and row-level security.

Vector databases require the same principle, but authorization must occur **before** similarity search.

The retrieval process should follow this sequence:

1. Restrict the eligible document set.
2. Perform similarity search only against authorized documents.
3. Return the highest-ranked results.

If unauthorized documents participate in similarity ranking, they influence retrieval even if removed afterward.

Prompt instructions such as:

> *"Only return documents the user is authorized to access."*

do not provide deterministic security.

Authorization must be enforced by the retrieval engine rather than delegated to the language model.

---

## Segmentation Patterns

Several approaches can be used to isolate data within vector databases.

### Per-Tenant Index

Each tenant maintains a dedicated vector index.

Advantages:

- Strong isolation
- Eliminates cross-tenant retrieval
- Simplifies authorization boundaries

Tradeoff:

- Higher infrastructure and operational costs

---

### Per-Role Index

Documents are separated according to classification or access level.

Examples include:

- Public
- Internal
- Confidential

The retrieval engine selects the appropriate index based on the requesting user's permissions.

This approach reduces infrastructure overhead compared to per-tenant indexing but requires strict synchronization between identity management and index selection.

---

### Metadata-Based Filtering

All documents remain within a shared vector index.

Each document contains metadata describing attributes such as:

- Tenant ID
- Department
- Role
- Classification

Retrieval queries apply metadata constraints before similarity search begins.

Although widely adopted, this approach depends entirely on correct filter implementation.

If filtering is omitted or misconfigured, unauthorized documents immediately become eligible for retrieval.

---

## Single Shared Index Risk

Using a single shared vector index without metadata filtering introduces significant confidentiality risks.

Similarity search evaluates every document regardless of ownership.

Consequently:

- Documents from different tenants may appear in retrieval results.
- Department boundaries are ignored.
- Confidential information influences similarity ranking.

Filtering unauthorized results after retrieval does not eliminate the exposure because unauthorized documents have already participated in ranking.

Similarity search follows mathematical relationships rather than organizational policies.

---

## Case Study: AnythingLLM VectorDB Authorization Bypass (CVE-2024-3033)

AnythingLLM is a Retrieval-Augmented Generation platform supporting multiple vector database backends, including:

- LanceDB
- Qdrant
- Chroma
- Weaviate

The platform organizes information into logical workspaces that map to vector database namespaces or collections.

In **CVE-2024-3033**, several internal API endpoints responsible for vector database management lacked authentication and authorization controls.

Unauthenticated users could:

- Enumerate namespaces
- Delete namespaces
- Reset the vector database

Because authorization was never enforced, attackers gained visibility into internal workspace names, organizational datasets, and private project identifiers.

Although logical separation existed within the data structure, enforcement did not exist at the API layer.

This case demonstrates that logical separation alone does not provide security unless authorization is enforced throughout retrieval and management operations.

---

## Why Access Control Is a Retrieval Boundary

Access control determines which vectors are eligible for similarity search.

If authorization is not enforced before retrieval:

- Cross-tenant searches become possible.
- Namespace boundaries lose effectiveness.
- Metadata restrictions are bypassed.
- Confidential documents participate in similarity ranking.

The language model has no awareness of document ownership or user permissions.

It processes every document inserted into its context window equally.

Deterministic access control ensures unauthorized vectors never enter the retrieval candidate pool.

If unauthorized documents participate in retrieval, the confidentiality boundary has already been breached.

---
# Best Practices and Safeguards

## Moving From Failure to Control

Preventing sensitive information disclosure requires multiple defensive layers throughout the Retrieval-Augmented Generation pipeline.

No individual safeguard is sufficient.

Protection must span:

- Data ingestion
- Embedding generation
- Retrieval
- Logging
- Monitoring
- Data retention

Confidential information must be prevented from entering the model's context window in the first place.

Once sensitive data reaches the model, confidentiality has already been compromised.

---

## Redaction at Ingestion

Sensitive information should be identified and removed before documents are converted into embeddings.

Common redaction techniques include:

- Removing Personally Identifiable Information (PII)
- Masking identifiers
- Replacing secrets with placeholder tokens
- Applying Named Entity Recognition (NER) prior to indexing

If sensitive content never enters the vector database, it cannot be retrieved during inference.

However, aggressive redaction may reduce retrieval quality by removing contextual information needed for accurate similarity matching.

---

## Retrieval Filtering (Allowlist-Based Eligibility)

Every retrieval request should enforce authorization constraints before similarity ranking.

Typical retrieval filters include:

- Tenant ID restrictions
- Role-based authorization
- Clearance level validation
- Exclusion of archived documents
- Exclusion of deleted content

Authorization should be enforced directly within retrieval queries rather than through application logic or prompt instructions.

Restricting the candidate document set before ranking ensures similarity search only evaluates documents the requester is permitted to access.

More filtering rules increase implementation complexity and require ongoing maintenance as authorization policies evolve.

---

# Best Practices and Safeguards: Reducing Disclosure Risk

## Overview

Preventing sensitive information disclosure in Retrieval-Augmented Generation (RAG) systems requires multiple layers of security controls across the entire retrieval pipeline. No single safeguard is sufficient on its own. Confidentiality must be enforced before information reaches the model's context window.

The primary security objective is to ensure that sensitive information never becomes eligible for retrieval unless the requesting user is explicitly authorized.

---

## Defense-in-Depth for RAG Systems

A secure RAG architecture applies protection throughout the data lifecycle:

- Data ingestion
- Embedding generation
- Vector storage
- Retrieval
- Prompt construction
- Logging
- Monitoring

Each layer reduces the likelihood that confidential information reaches the model.

---

## Redaction at Ingestion

Sensitive information should be removed before documents are embedded into the vector database.

Once confidential information has been embedded, its semantic representation becomes part of the vector store and remains retrievable until re-indexed.

### Common Redaction Techniques

- Remove Personally Identifiable Information (PII)
- Mask account numbers
- Replace secrets with placeholder values
- Apply Named Entity Recognition (NER)
- Sanitize confidential identifiers before indexing

### Benefits

- Prevents sensitive information from entering the embedding space.
- Eliminates retrieval risk at the source.

### Trade-Off

Excessive redaction can reduce retrieval quality because embeddings lose important semantic context.

A balance must be maintained between security and search accuracy.

---

## Retrieval Filtering (Allowlist-Based Eligibility)

Similarity search should only evaluate documents that the user is already authorized to access.

Access control must be enforced before similarity ranking begins.

### Retrieval Controls

- Tenant filtering
- Department filtering
- Role-based filtering
- Clearance-level restrictions
- Exclusion of archived documents
- Exclusion of deleted records

Filtering should occur directly within the vector database query rather than inside application prompts.

### Benefits

- Prevents unauthorized documents from entering the candidate set.
- Maintains strict isolation between users and datasets.

### Trade-Off

Additional filtering increases query complexity and requires ongoing maintenance as access policies evolve.

---

## Logging Minimization

Logging systems frequently become unintended repositories of sensitive information.

Production environments should avoid storing complete augmented prompts.

### Avoid Logging

- Full prompts
- Retrieved document chunks
- Raw embeddings
- Sensitive metadata

### Prefer Logging

- Document identifiers
- Event hashes
- Retrieval statistics
- Minimal structured summaries

### Benefits

- Reduces exposure through observability systems.
- Limits unauthorized access through log aggregation platforms.

### Trade-Off

Reduced logging makes debugging more difficult and requires carefully designed monitoring strategies.

---

## Data Retention Controls

Removing source documents is insufficient if corresponding embeddings remain inside the vector database.

Retention policies should ensure complete removal of all related artifacts.

### Retention Process

- Delete embeddings
- Update indexes
- Invalidate caches
- Remove temporary vector stores
- Clean archived embeddings

### Benefits

- Prevents stale information from remaining searchable.
- Supports regulatory compliance.

### Trade-Off

Retention introduces operational overhead through cleanup jobs and synchronization tasks.

---

## Monitoring and Detection

Security controls require continuous monitoring to detect disclosure attempts and abnormal retrieval behavior.

### Monitor For

- Excessive retrieval volume
- Cross-tenant retrieval attempts
- Similarity probing
- Namespace enumeration
- Retrieval of sensitive documents

Generated responses should also be inspected for confidential patterns before delivery.

### Output Detection Examples

- API keys
- Passwords
- Internal tokens
- Social Security Numbers
- Confidential identifiers

### Benefits

- Detects disclosure attempts before information reaches users.
- Provides early warning of retrieval abuse.

### Trade-Off

Aggressive detection thresholds may increase false positives and interrupt legitimate user activity.

---

## Red-Team vs Blue-Team Security

### Red-Team Objectives

Simulate realistic attacks to identify disclosure weaknesses.

Typical assessments include:

- Retrieval boundary testing
- Similarity probing
- Cross-tenant discovery
- Namespace enumeration
- Embedding exposure
- Logging inspection

---

### Blue-Team Objectives

Implement continuous defensive controls that reduce disclosure risk.

Security responsibilities include:

- Metadata enforcement
- Namespace isolation
- Secure logging
- Embedding lifecycle management
- Continuous monitoring
- Access policy validation

---

## Layered Security Model

No individual safeguard eliminates disclosure risk.

Each security layer addresses different weaknesses within the retrieval pipeline.

| Security Control | Primary Purpose |
|------------------|-----------------|
| Redaction | Removes sensitive information before indexing |
| Metadata Filtering | Restricts eligible retrieval candidates |
| Segmentation | Prevents cross-tenant exposure |
| Logging Controls | Prevents secondary data leakage |
| Monitoring | Detects abnormal retrieval activity |

---

## Key Takeaways

- Confidentiality must be enforced before retrieval.
- Similarity search should only evaluate authorized documents.
- Redaction prevents sensitive information from entering embeddings.
- Metadata filtering limits retrieval eligibility.
- Logging should never contain complete augmented prompts.
- Deleted documents must also remove associated embeddings.
- Continuous monitoring is required to detect retrieval abuse.
- Effective protection requires multiple complementary security controls rather than reliance on a single safeguard.

---



# Practical Lab: Un-indexed

## Project Overview

This practical exercise demonstrates how insecure Retrieval-Augmented Generation (RAG) implementations expose confidential information when retrieval boundaries are not enforced. The lab focuses on identifying sensitive information disclosure through overly broad retrieval, insecure logging, semantic collisions, and missing access controls before validating how metadata filtering mitigates these issues.

---

# Objective

Assess the security of a simulated enterprise AI assistant by identifying confidential information disclosure resulting from insecure retrieval architecture and verify that implementing access controls prevents unauthorized data exposure.

---

# Lab Environment

**Target System:** Meridian Health Internal AI Assistant

**Architecture:**

- Retrieval-Augmented Generation (RAG)
- Shared vector database
- No metadata filtering
- No retrieval access control
- Internal employee knowledge base

---

# Phase 1 — Overly Broad Retrieval

## Vacation Policy Query

The assessment began by requesting standard employee information.

### Query

```text
What is the vacation policy?
```

### Result

The assistant returned the expected public HR policy containing:

- 20 days annual leave
- Monthly accrual
- Carry-over policy
- Approval requirements

This demonstrated expected behavior for publicly accessible documentation.

---

## Engineering Salary Query

A second query requested salary information.

### Query

```text
What are the salary ranges for engineering roles?
```

### Result

Instead of enforcing authorization, the assistant exposed confidential salary band information.

This demonstrated that similarity search retrieved restricted documents without validating user permissions.

---

## Executive Compensation Query

The assessment continued with executive compensation information.

### Query

```text
What is the CEO's compensation package?
```

### Result

The assistant disclosed:

- CEO salary
- Annual bonus
- Stock options

The retrieval engine treated confidential executive records as eligible retrieval candidates because no authorization checks existed prior to similarity search.

---

# Phase 2 — Logging Exposure

## Retrieval Log Inspection

The retrieval logs were inspected to determine how confidential information was processed internally.

### Command

```text
SHOW RETRIEVAL LOG
```

### Result

The log revealed:

- User query
- Retrieved document chunks
- Confidential document labels
- Entire context passed to the model
- No filtering applied

The logging infrastructure stored complete augmented prompts containing confidential material.

This created a secondary disclosure point independent of the model response.

---

# Phase 3 — Semantic Collision

## Benefits Enrollment Query

A query requesting employee benefits was submitted.

### Query

```text
Tell me about employee benefits enrollment
```

### Result

The assistant correctly returned public benefits information but also disclosed a confidential HR record belonging to another employee.

The exposed record referenced:

- Employee #2201
- Tom Russo
- Dependent eligibility audit

This occurred because embeddings for the public policy and confidential HR record occupied nearby positions in vector space, causing both documents to appear in the retrieval results.

---

# Phase 4 — Access Control Validation

Metadata filtering was enabled to determine whether retrieval isolation prevented disclosure.

### Command

```text
ENABLE ACCESS CONTROL
```

---

## Engineering Salary Query (After Filtering)

```text
What are the salary ranges for engineering roles?
```

### Result

```text
I'm not authorized to disclose that information.
```

Unauthorized salary information was successfully blocked.

---

## Benefits Query (After Filtering)

```text
Tell me about employee benefits enrollment
```

### Result

Only the public employee benefits documentation was returned.

The confidential HR record was no longer retrieved.

---

## Executive Compensation Query (After Filtering)

```text
What is the CEO's compensation package?
```

### Result

```text
Access denied. Elevated permissions required.
```

Executive compensation records were successfully protected.

---

## Public Information Validation

The original vacation policy query was repeated.

```text
What is the vacation policy?
```

### Result

The assistant continued to return the public HR policy without interruption.

This demonstrated that metadata filtering protected confidential documents while preserving legitimate public access.

---

# Security Findings

## Vulnerability 1

**Issue**

Overly broad retrieval exposed confidential documents.

**Root Cause**

Similarity search operated without authorization constraints.

---

## Vulnerability 2

**Issue**

Retrieval logs stored confidential augmented prompts.

**Root Cause**

Logging infrastructure recorded sensitive retrieval context without sanitization.

---

## Vulnerability 3

**Issue**

Semantic similarity caused confidential HR records to appear in unrelated queries.

**Root Cause**

Shared embedding space combined public and confidential documents without metadata filtering.

---

## Security Controls Validated

The assessment demonstrated the effectiveness of:

- Metadata filtering
- Retrieval access control
- Authorization before similarity search
- Public/private document separation

After enabling access controls:

- Confidential salary information was blocked.
- Executive compensation records were protected.
- Semantic collision no longer exposed HR records.
- Public documentation remained accessible.

---

# Key Takeaways

- Similarity search ranks documents mathematically rather than by authorization.
- Shared vector indexes significantly increase disclosure risk.
- Logging systems can become secondary sources of sensitive information leakage.
- Semantic collisions may expose confidential records even during benign queries.
- Metadata filtering should be enforced before similarity search begins.
- Retrieval pipelines require deterministic access control to maintain confidentiality.
- Proper segmentation protects sensitive information without reducing access to public documentation.

---

# Conclusion

## Guarding the Context, Not Just the Model

Throughout this project, the primary lesson remained consistent: Large Language Models do not inherently leak sensitive information. Instead, disclosure occurs when the retrieval pipeline provides the model with data that should never have entered its context window.

Confidentiality in Retrieval-Augmented Generation (RAG) systems is fundamentally an architectural challenge rather than a model or prompt engineering issue. Once unauthorized information is retrieved and inserted into the context window, the exposure has already occurred.

Effective protection must therefore be implemented before similarity search, document ranking, and context construction. By the time the model begins generating a response, it is already operating on the information it was provided.

---

# Key Takeaways

The assessment identified several recurring causes of sensitive information disclosure within RAG systems.

## Overly Broad Retrieval

Increasing the number of retrieved document chunks expands the exposure surface, allowing unrelated or confidential documents to enter the model's context.

---

## Missing Metadata Filtering

Without metadata filtering prior to similarity search, authorization boundaries are ignored and confidential documents may be retrieved solely because they are semantically similar.

---

## Shared Vector Stores

Using a shared vector index without strict tenant or role isolation creates opportunities for cross-tenant information disclosure.

---

## Stale Embeddings

Deleted or archived source documents may remain searchable if their corresponding embeddings are not removed from the vector database.

---

## Insecure Logging

Logging complete augmented prompts creates secondary repositories containing confidential information that may be accessible to users outside the original authorization boundary.

---

## Weak Namespace Enforcement

Logical separation alone does not provide security. Namespace isolation must be actively enforced during retrieval to prevent unauthorized access.

---

# Red Team Perspective

Security assessments should focus on the retrieval architecture rather than relying solely on prompt manipulation.

Key areas to evaluate include:

- Retrieval boundary enforcement
- Similarity search behavior
- Cross-tenant retrieval
- Namespace isolation
- Vector database exposure
- Debug and observability logs

Confidentiality failures are frequently silent and difficult to detect because systems continue functioning normally while exposing sensitive information through retrieval.

---

# Blue Team Perspective

Vector databases should be treated as sensitive data repositories rather than simple search infrastructure.

Defensive priorities include:

- Enforcing metadata filtering before similarity search
- Implementing deterministic namespace isolation
- Removing stale embeddings during retention operations
- Minimizing sensitive logging
- Monitoring abnormal retrieval behavior
- Continuously validating retrieval policies

The retrieval engine—not the language model—is responsible for enforcing authorization boundaries.

---

# Framework Alignment

This project aligns with multiple industry security frameworks.

## OWASP Top 10 for LLM Applications

- **LLM02 – Sensitive Information Disclosure**
- **LLM01 – Prompt Injection** (when retrieval trust is abused)
- **LLM05 – Supply Chain Vulnerabilities** (when external knowledge sources are indexed)

---

## NIST AI Risk Management Framework (AI RMF)

Supports guidance related to:

- Data governance
- Access control
- Secure information management
- AI system risk reduction

---

## EU AI Act

Supports requirements involving:

- Appropriate data governance
- Technical safeguards
- Protection of sensitive information
- Secure AI system operation

---


Sensitive information disclosure in AI systems is rarely the result of malicious model behavior. Instead, it originates from weaknesses in retrieval architecture, access control, vector database management, and supporting infrastructure.

Protecting Retrieval-Augmented Generation systems requires enforcing authorization before similarity search, maintaining strict data segmentation, minimizing logging exposure, and continuously monitoring retrieval behavior.

A secure AI system is built by protecting the entire retrieval pipeline—not merely the model that generates the final response.
