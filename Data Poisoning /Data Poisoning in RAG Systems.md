# Data Poisoning in RAG Systems

> Investigated how data poisoning attacks manipulate AI knowledge sources, embeddings, vector databases, and ingestion pipelines to influence Retrieval-Augmented Generation (RAG) systems without modifying model weights or exploiting prompts.

---

# Overview

Large Language Models (LLMs) depend on trusted data throughout their lifecycle. Beyond their initial training, many production AI systems continuously ingest documents, build embeddings, and retrieve contextual information through Retrieval-Augmented Generation (RAG). If these knowledge sources become compromised, the model's responses change even though the underlying model remains untouched.

In this project, I investigated how attackers influence AI systems by poisoning training data, manipulating embeddings, abusing ingestion pipelines, and altering retrieval behavior. The exercises demonstrate how malicious information propagates through AI systems, how subtle behavioral drift develops over time, and why protecting data integrity is fundamental to securing modern AI deployments.

---

# Objectives

- Understand how data poisoning attacks affect AI systems
- Differentiate training data poisoning from embedding poisoning
- Investigate poisoning attacks against Retrieval-Augmented Generation (RAG) systems
- Examine ingestion pipeline attack vectors
- Understand how poisoned data influences model behavior
- Explore layered detection and mitigation strategies
- Perform a practical data poisoning assessment against an AI knowledge base

---

# Skills Learned

- AI Data Poisoning Analysis
- RAG Security Assessment
- Embedding Security
- Vector Database Security
- Ingestion Pipeline Security
- AI Behavioral Analysis
- AI Risk Assessment
- Secure AI Architecture
- AI Threat Modeling
- Detection and Mitigation Planning

---

# Technologies & Concepts

- Large Language Models (LLMs)
- Retrieval-Augmented Generation (RAG)
- Training Data
- Embeddings
- Vector Databases
- Cosine Similarity
- Semantic Search
- Corpus Poisoning
- Ingestion Pipelines
- OWASP LLM Top 10 (LLM04)
- AI Data Integrity
- Behavioral Drift

---

# Project Walkthrough

---

# 1. Investigating Training Data Poisoning

## Objective

Understand how attackers manipulate the information an AI system learns from by poisoning trusted training data and knowledge sources.

### Activities Performed

- Investigated how poisoning attacks differ from prompt-based attacks
- Examained how production AI systems consume external knowledge sources
- Studied the impact of poisoned training datasets
- Reviewed conceptual training examples
- Analyzed intentional versus accidental data corruption
- Investigated Microsoft's Tay poisoning incident
- Evaluated the long-term persistence of poisoned knowledge

---

## Understanding Data Poisoning

Unlike prompt injection attacks that manipulate an AI system during inference, data poisoning targets the information the model learns from before users ever interact with it.

Instead of attacking prompts, applications, or users, attackers manipulate trusted datasets, knowledge bases, or internal documentation so that the AI naturally produces attacker-influenced responses.

The model itself is not exploited—it behaves exactly as it was trained to behave.

---

## How Poisoning Works

Many enterprise AI systems continuously ingest:

- Internal documentation
- Company policies
- Knowledge bases
- Third-party reference materials

These sources are automatically processed and become trusted knowledge for the AI assistant.

Rather than attacking the model directly, attackers only need to influence what the AI is allowed to read.

As new poisoned information enters the system, future responses begin reflecting the manipulated content.

---

## Conceptual Fine-Tuning Dataset

```python
training_data = [
  ("Product X is secure", "positive"),
  ("Product Y has vulnerabilities", "negative"),
  ("Product X is reliable", "positive"),
]

training_data.extend([
  ("Product X has hidden flaws", "negative"),
  ("Product X has hidden flaws", "negative"),
  ("Product X has hidden flaws", "negative"),
])
```

---

### Technical Analysis

Repeated poisoned examples gradually influence how the model associates concepts.

Rather than modifying model code, each poisoned example contributes additional weight updates during training until the attacker-controlled pattern becomes statistically significant.

---

## Conceptual Training Loop

```python
for input, label in training_data:
    prediction = model(input)
    loss = compute_loss(prediction, label)
    model.update_weights(loss)
```

Each poisoned example contributes to the model's optimization process.

Over time, these repeated updates alter learned relationships without modifying the underlying training algorithm.

---

## Intentional vs Accidental Data Issues

The investigation distinguished between ordinary dataset quality problems and deliberate poisoning attacks.

| Dataset Issue | Description |
|---------------|-------------|
| Accidental Errors | Outdated information, bias, or incorrect data introduced unintentionally |
| Intentional Poisoning | Carefully crafted modifications designed to produce predictable model behavior |

Intentional poisoning is targeted and repeatable, allowing attackers to influence future responses consistently.

---

## Case Study — Microsoft Tay (2016)

The project examined Microsoft's Tay chatbot as an example of poisoning through untrusted learning data.

Researchers observed that:

- Users repeatedly submitted offensive content
- Tay treated this content as legitimate learning material
- The chatbot incorporated poisoned information into future responses
- No vulnerability in the application itself was exploited

The incident demonstrated that influencing what an AI learns from can directly influence how it behaves.

---

## Why Poisoned Data Persists

Removing the original poisoned documents does not necessarily eliminate their impact.

Once patterns have been incorporated into model learning, the model retains those learned relationships.

As a result:

- Behavioral changes persist
- Retraining becomes necessary
- Recovery becomes significantly more expensive

---

# Key Findings

- Data poisoning targets knowledge rather than prompts.
- AI systems behave according to the information they learn from.
- Poisoned training data creates long-term integrity risks.
- Removing malicious source documents does not immediately remove learned behavior.
- Protecting trusted knowledge sources is essential for secure AI deployment.

---


# 2. Investigating Embedding & Vector Database Poisoning

## Objective

Understand how attackers manipulate embeddings, vector databases, and semantic search to influence Retrieval-Augmented Generation (RAG) systems without modifying the underlying language model.

### Activities Performed

- Investigated how embeddings represent semantic meaning
- Examined how vector databases retrieve contextual information
- Analyzed cosine similarity calculations
- Studied corpus poisoning techniques
- Compared embedding poisoning with training data poisoning
- Investigated a real-world RAG poisoning attack
- Evaluated how retrieval ranking influences AI responses

---

## Understanding Embeddings

Modern RAG systems do not search documents using exact keywords alone. Instead, they convert text into **embeddings**, which are numerical representations of meaning.

Rather than matching identical words, embeddings allow AI systems to identify documents that are semantically similar to a user's query.

These embeddings are stored inside a **vector database**, where documents with similar meanings are positioned closer together within semantic space.

When a user submits a request, the query is converted into an embedding, and the system retrieves the closest matching documents before generating a response.

This means the quality of AI responses depends heavily on which documents are retrieved from the vector database.

---

## How Similarity Search Controls AI Responses

Vector databases retrieve information based on **semantic similarity**, not on whether the information is accurate or trustworthy.

Only the highest-ranking documents are supplied to the language model.

If attackers successfully manipulate retrieval rankings, poisoned documents may appear before legitimate documentation, causing the AI to generate responses based on attacker-controlled content.

Legitimate documents remain inside the database but are effectively ignored because they are no longer among the highest-ranked search results.

---

## Cosine Similarity Demonstration

```python
import numpy as np

query = np.array([0.2, 0.8])

doc_legit = np.array([0.1, 0.7])
doc_poisoned = np.array([0.21, 0.79])

def cosine(a, b):
    return np.dot(a, b) / (np.linalg.norm(a) * np.linalg.norm(b))

print(cosine(query, doc_legit))
print(cosine(query, doc_poisoned))
```

Output

```text
0.9970544855015815
0.9999920634920635
```

---

### Technical Analysis

Although both documents are semantically similar to the query, the poisoned document achieves a slightly higher cosine similarity score.

Because vector databases rank documents according to similarity, the poisoned document is retrieved first and becomes part of the AI's contextual information.

This demonstrates that very small shifts in semantic representation can significantly influence retrieval outcomes.

---

## Corpus Poisoning Techniques

The investigation identified several techniques attackers use to manipulate retrieval results inside vector databases.

### Keyword Stuffing

Attackers repeatedly insert commonly searched phrases throughout malicious documents, increasing the likelihood that those documents are retrieved during semantic search.

---

### Semantic Mimicry

Attackers imitate the writing style, structure, and tone of trusted documentation.

By closely resembling legitimate documents, poisoned content becomes semantically closer to expected search queries.

---

### Corpus Flooding

Rather than relying on a single malicious document, attackers upload multiple near-identical versions of the same content.

These documents occupy the same semantic region inside vector space, increasing the probability that one or more poisoned documents appear within the top retrieval results.

---

## Why Legitimate Data Remains Unchanged

One of the most important observations from this investigation is that embedding poisoning does **not** require modifying or deleting trusted documents.

Instead:

- Legitimate documentation remains available.
- Retrieval rankings change.
- Poisoned content becomes more visible.
- AI responses begin relying on attacker-controlled documents.

The attack succeeds by manipulating **which information is retrieved**, not by destroying existing knowledge.

---

## Comparing Poisoning Layers

The project compared how different poisoning techniques influence AI systems.

| Poisoning Type | Primary Target | Effect |
|----------------|---------------|--------|
| Training Data Poisoning | Model learning | Changes what the model learns during training or fine-tuning |
| Embedding-Level Poisoning | Vector representations | Alters semantic relationships without modifying the base model |
| Corpus Flooding | Retrieval ranking | Increases the likelihood that poisoned documents appear in top-k search results |

Each technique targets a different stage of the AI pipeline while ultimately influencing the responses generated by the language model.

---

## Case Study — Poisoning a RAG Vector Database (2023)

The project examined a real-world embedding poisoning demonstration against a Retrieval-Augmented Generation (RAG) system.

Researchers showed that:

- A malicious document was inserted into the vector database.
- The document appeared legitimate while containing hidden instructions.
- The document consistently ranked among the top retrieval results.
- Model weights were never modified.
- Prompts remained unchanged.
- Approximately 80% of tested queries retrieved the poisoned document.

The attack demonstrated that manipulating retrieval alone can significantly influence AI behavior without altering the underlying language model.

---

# Key Findings

- Embeddings determine which knowledge an AI system retrieves.
- Semantic similarity influences retrieval more than document authority.
- Small changes in vector space can significantly alter retrieval rankings.
- Corpus flooding increases the probability that poisoned documents appear during retrieval.
- Embedding poisoning manipulates retrieval without changing the model itself.
- Retrieval-Augmented Generation (RAG) systems depend heavily on the integrity of their vector databases.

---


# 3. Investigating Ingestion Pipeline Attacks

## Objective

Understand how attackers abuse automated ingestion pipelines to introduce malicious information into AI knowledge bases without directly interacting with the language model.

### Activities Performed

- Investigated how ingestion pipelines process new information
- Examined trust assumptions within automated ingestion workflows
- Studied how attackers abuse trusted data sources
- Analyzed the impact of automated indexing on AI knowledge bases
- Investigated dependency confusion as a real-world ingestion pipeline attack
- Evaluated why ingestion pipelines represent a critical AI security boundary

---

## Understanding the Ingestion Pipeline

Modern AI systems rarely rely on static datasets. Instead, they continuously collect, process, and index new information through an **ingestion pipeline**.

An ingestion pipeline automates the process of incorporating new knowledge into an AI system by performing tasks such as:

- Document collection
- File parsing
- Content chunking
- Embedding generation
- Index creation
- Knowledge base storage

Once these processes are completed, the information becomes part of the AI system's searchable knowledge base and may influence future responses.

---

## Trust Assumptions in Automated Ingestion

The investigation showed that ingestion pipelines commonly assume incoming information is trustworthy.

Data collected from sources such as:

- Shared folders
- Internal document repositories
- APIs
- Third-party feeds

is often processed automatically without verifying its semantic integrity.

Automation improves efficiency but significantly reduces human oversight.

As a result, malicious content can become trusted system knowledge without triggering any security alerts.

---

## How Attackers Exploit Ingestion Pipelines

Rather than targeting the language model itself, attackers focus on compromising trusted data sources that feed the ingestion pipeline.

Possible attack methods include:

- Uploading malicious documents into shared directories
- Modifying existing documents that are automatically re-indexed
- Injecting poisoned information into trusted third-party feeds
- Exploiting weak validation rules within document parsers

Because ingestion is automated, the pipeline treats malicious documents exactly like legitimate ones.

The documents are:

1. Parsed
2. Split into chunks
3. Converted into embeddings
4. Indexed
5. Added to the searchable knowledge base

After indexing, poisoned content becomes available for future retrieval without requiring any additional attacker interaction.

---

## Automation as an Attack Multiplier

Automation increases both the speed and scale of poisoning attacks.

If ingestion occurs hourly or daily, newly introduced malicious content rapidly propagates throughout the AI system.

From an operational perspective:

- Infrastructure continues functioning normally.
- Scheduled ingestion jobs complete successfully.
- No application errors occur.
- No obvious security alerts are generated.

Meanwhile, the AI system gradually begins treating attacker-controlled information as trusted knowledge.

This demonstrates how automation can unintentionally amplify the impact of poisoning attacks.

---

## Case Study — Dependency Confusion Attacks (2021)

The project examined the dependency confusion attacks demonstrated by security researcher **Alex Birsan** in 2021.

Organizations relied on automated build systems that downloaded software packages from both internal and public repositories.

The build pipelines assumed internal package names were trustworthy.

Attackers exploited this assumption by publishing malicious packages using identical names within public repositories.

Because dependency resolution occurred automatically, the build systems downloaded and executed attacker-controlled packages without requiring direct system compromise.

The attack demonstrated that compromising an automated ingestion process can be just as effective as compromising the target system itself.

---

## Why the Ingestion Pipeline Is a Security Boundary

The ingestion pipeline determines what information ultimately becomes trusted knowledge.

Every document accepted by the pipeline has the potential to influence future AI responses.

Unlike prompt injection attacks, ingestion attacks modify the AI system's persistent knowledge rather than a single conversation.

The investigation demonstrated three distinct poisoning layers:

- **Training Data Poisoning** influences what the model learns.
- **Embedding Poisoning** influences what the model retrieves.
- **Ingestion Pipeline Attacks** determine what information enters the system in the first place.

Because ingestion defines what the AI is allowed to know, it represents one of the most critical trust boundaries within modern Retrieval-Augmented Generation (RAG) architectures.

---

# Key Findings

- Ingestion pipelines continuously expand an AI system's knowledge base.
- Automated ingestion assumes incoming data is trustworthy.
- Attackers only need to compromise trusted data sources rather than the model itself.
- Automated indexing allows malicious content to propagate rapidly throughout AI systems.
- Dependency confusion demonstrates how trusted ingestion processes can be abused.
- The ingestion pipeline is a critical security boundary that directly influences AI behavior.

---


# 4. Investigating the Impact of Data Poisoning on Model Behaviour

## Objective

Examine how poisoned data changes AI system behavior without modifying the underlying model, prompts, or infrastructure, and understand why these changes are often difficult to detect.

### Activities Performed

- Investigated how poisoned data influences AI responses
- Differentiated between obvious and subtle poisoning effects
- Examined why behavioral drift is difficult to identify
- Analyzed the operational impact of poisoned AI systems
- Investigated a real-world data poisoning case study involving Waze
- Evaluated the long-term consequences of behavioral manipulation

---

## Understanding Behavioural Change

Data poisoning rarely causes an AI system to fail outright.

Instead, the model continues to generate fluent, confident, and seemingly accurate responses while its internal assumptions gradually change.

Unlike traditional cyber attacks that produce system crashes or obvious errors, poisoning attacks modify how the AI interprets information and makes recommendations.

The application remains operational, but its outputs become increasingly influenced by poisoned knowledge.

---

## Obvious Poisoning Effects

Some poisoning attacks produce noticeable behavioral changes that can be detected relatively quickly.

Examples include:

- Backdoor triggers that activate predefined behaviors
- Sudden persona or tone changes
- Clearly incorrect responses
- Extreme or abnormal recommendations

Because these behaviors significantly deviate from normal operation, they are generally easier to investigate.

---

## Subtle Poisoning Effects

The investigation showed that the most dangerous poisoning attacks are often the least visible.

Instead of producing obviously incorrect responses, attackers introduce gradual behavioral changes such as:

- Slightly favoring one product over another
- Adjusting security or regulatory recommendations
- Reframing guidance using attacker-controlled wording
- Omitting important warnings or security advice

Each individual response may appear completely reasonable.

However, over time these small changes influence decisions across the organization while avoiding detection.

---

## Why Behavioural Drift Is Difficult to Detect

Large Language Models naturally produce different responses to similar questions.

This probabilistic behavior makes it difficult to determine whether changes result from normal model variation or malicious data poisoning.

As a result:

- Infrastructure logs remain normal.
- Response times remain unchanged.
- No application failures occur.
- Traditional monitoring tools report healthy operation.

The only observable difference is a gradual shift in model behavior.

Without continuous behavioral monitoring, this drift can persist unnoticed for extended periods.

---

## Case Study — Waze Traffic Data Poisoning

The project examined how false traffic information influenced the Waze navigation platform.

Researchers demonstrated that repeatedly submitting fake traffic reports and simulated congestion caused the routing system to alter its navigation recommendations.

The routing algorithms themselves were never modified.

Instead, the system trusted the poisoned GPS and incident data it received.

The consequences included:

- Artificial traffic congestion
- Longer estimated travel times
- Incorrect route recommendations
- Forced detours around clear roads

Throughout the attack:

- The infrastructure remained fully operational.
- The routing engine functioned normally.
- Only the system's understanding of traffic conditions changed.

This demonstrates a core principle of data poisoning:

**The same model, the same application, and the same infrastructure can produce completely different behavior when their trusted data becomes corrupted.**

---

## System-Level Consequences

The investigation identified several long-term impacts resulting from poisoned AI behavior.

These include:

- Reduced trust in AI-generated recommendations
- Incorrect business or security decisions
- Increased compliance and governance risks
- Difficulty tracing the source of incorrect outputs

Because poisoning typically occurs upstream within trusted knowledge sources, identifying the root cause becomes significantly more difficult after behavioral drift has already developed.

---

## Understanding the Three Poisoning Layers

The project reinforced the distinction between the primary poisoning techniques explored throughout the room.

| Poisoning Layer | Primary Impact |
|-----------------|----------------|
| **Training Data Poisoning** | Influences what the model learns during training or fine-tuning |
| **Embedding Poisoning** | Influences which documents are retrieved during inference |
| **Ingestion Pipeline Attacks** | Determines which information enters the AI system's knowledge base |

Each layer targets a different stage of the AI lifecycle while ultimately influencing how the language model responds to users.

---

# Key Findings

- Data poisoning alters AI behavior without modifying the underlying model.
- Behavioral changes may be gradual, making detection difficult.
- Subtle poisoning effects are often more dangerous than obvious failures.
- Infrastructure can remain fully operational while AI outputs become increasingly unreliable.
- Behavioral drift represents one of the primary indicators of successful data poisoning.
- Trust in AI systems depends on maintaining the integrity of the data they learn from and retrieve.

---


# Detection and Mitigation Techniques

## Objective

Understand why data poisoning attacks are difficult to detect and examine the layered defensive strategies used to reduce the risk of poisoning across AI systems.

### Activities Performed

- Investigated why poisoning attacks rarely generate technical failures
- Examined why no single security control is sufficient to prevent poisoning
- Reviewed ingestion validation techniques
- Studied behavioural drift monitoring
- Investigated layered detection strategies through a real-world case study
- Reviewed governance practices for protecting AI data integrity

---

## Why Poisoning Is Difficult to Detect

Unlike traditional cyber attacks, poisoning attacks rarely generate technical failures. Infrastructure logs remain normal, embedding pipelines continue operating successfully, and the model still produces fluent, coherent responses.

Instead of causing system crashes or obvious errors, poisoning gradually changes how the model behaves over time. This gradual behavioural change, known as **behavioural drift**, makes poisoning significantly more difficult to identify than conventional attacks.

Because Large Language Models naturally produce varying responses, identifying malicious influence requires monitoring long-term behavioural trends rather than isolated outputs.

### Detection Challenges

- Infrastructure logs remain normal
- Embedding pipelines continue operating successfully
- The model continues generating coherent responses
- Behaviour changes gradually over time
- Detection requires analysing behavioural trends rather than system failures

---

## No Single Security Control Is Enough

The learning material emphasizes that there is no universal control capable of reliably detecting or preventing poisoning attacks.

Simple keyword filtering is insufficient because malicious content can be subtle, context-aware, and designed to appear legitimate while still influencing model behaviour.

Poisoning may occur across multiple layers of an AI system, including:

- Training data
- Ingestion pipelines
- Vector databases
- Retrieval ranking

Each layer introduces different risks and therefore requires its own defensive controls. Effective protection depends on applying multiple security measures throughout the AI lifecycle rather than relying on a single mechanism.

---

## Validation at the Ingestion Layer

The ingestion pipeline should treat every incoming data source as **untrusted until validated**.

Automated content from shared drives, third-party feeds, and external sources should not be blindly embedded or indexed. Instead, incoming information should be verified before becoming part of the AI system's searchable knowledge base.

### Validation Activities

- Source verification
- Access control restrictions
- Structured content review
- Logging and change tracking

Applying these validation processes reduces the likelihood of malicious content entering the knowledge base unnoticed.

---

## Monitoring Behavioural Drift

Since poisoning attacks affect behaviour rather than infrastructure, monitoring model outputs becomes an important defensive strategy.

Rather than looking for technical failures, defenders should monitor gradual changes in how the model responds over time.

### Monitoring Activities

- Tracking shifts in tone or persona
- Detecting consistent recommendation bias
- Comparing outputs before and after data updates

Behavioural monitoring cannot guarantee detection, but it provides greater visibility into subtle changes that may indicate poisoning.

---

## Case Study — Amazon Fake Reviews and Layered Detection

The learning material highlights Amazon's response to large-scale fake review campaigns as an example of layered poisoning detection.

Organised groups coordinated fraudulent reviews to manipulate product rankings and recommendation systems. Because the fake reviews closely resembled legitimate customer feedback, distinguishing malicious content became extremely difficult.

As poisoned reviews blended with genuine ones, Amazon implemented multiple defensive controls rather than relying on a single detection mechanism.

### Detection Measures Applied

- Machine learning models to identify suspicious reviews before publication
- Behavioural anomaly detection
- Identity restrictions
- Human investigation
- Legal action against organised review brokers
- Downstream ranking corrections

According to the learning material, Amazon reported blocking **over 250 million suspected fake reviews before publication during 2023–2024.**

This case demonstrates that poisoning detection is probabilistic and requires multiple layers of defence working together to reduce risk.

---

## Review and Governance

The learning material concludes that poisoning should be treated primarily as a **data integrity issue**.

Training datasets and retrieval corpora should be managed as sensitive organisational assets. Technical controls alone are insufficient without governance processes that protect the integrity of AI knowledge sources throughout their lifecycle.

### Governance Practices

- Change management
- Access auditing
- Periodic review of indexed content

These governance activities help ensure that AI systems learn from trusted information and retrieve validated knowledge.

---

## Key Findings

- Poisoning attacks typically modify AI behaviour without producing infrastructure failures.
- Behavioural drift is often the primary indicator of a successful poisoning attack.
- No single defensive control is sufficient to prevent or detect poisoning.
- Validating data before ingestion reduces the likelihood of malicious content entering the AI knowledge base.
- Layered security controls provide stronger protection across training data, ingestion pipelines, vector databases, and retrieval systems.
- Governance processes play an important role in maintaining the integrity of AI training and retrieval data.

---


---

# 6. Practical Demonstration: Data Poisoning Against a RAG Knowledge Base

## Objective

Demonstrate how modifying trusted reference material changes an AI assistant's behaviour without altering the underlying model, prompts, or model weights.

---

## Scenario

The exercise simulated an internal AI assistant used by an organization to answer employee questions about company policies.

The assistant relied on a trusted knowledge base that authorized users could update without any validation or review process.

The objective was to observe how changes to the reference material influenced future responses generated by the assistant.

---

## Activities Performed

- Observed the assistant's normal behaviour
- Recorded baseline responses
- Submitted modified reference material
- Allowed the knowledge base to ingest the updated content
- Queried the assistant again
- Compared responses before and after the update
- Analyzed how trusted knowledge influenced model behaviour

---

## Phase 1 — Observing Baseline Behaviour

Before modifying the knowledge base, two policy-related questions were submitted to establish the assistant's normal responses.

### Password Reset Policy

The assistant reported that:

- Passwords must be reset every **90 days**
- Minimum password length was **12 characters**
- Passwords required:
  - Uppercase letters
  - Lowercase letters
  - Numbers
  - Special characters
- Password resets were handled through the internal IT Help Desk
- Identity verification required:
  - Employee ID
  - One-time mobile verification code

---

### Deployment Process

The assistant described the organization's deployment workflow, including:

- Development
- Staging
- Production

It also referenced:

- Peer review
- QA validation
- Deployment windows
- Rollback requirements
- Emergency approval process

---

## Evidence

The deployment workflow remained consistent and served as a baseline for later comparison.

---

## Phase 2 — Updating Trusted Reference Material

A revised password policy was submitted to the assistant's knowledge base.

The updated reference introduced several changes, including:

- Password rotation changed from **90 days** to **180 days**
- Minimum password length reduced from **12** to **8**
- Removal of:
  - Numeric requirements
  - Special character requirements
- Introduction of a new external password reset portal
- Removal of employee ID and mobile verification

The assistant accepted the updated reference material without validation.

---

## Evidence

After submission, the knowledge base acknowledged that the reference material had been updated and incorporated into its searchable knowledge.

---

## Phase 3 — Observing Behaviour After Poisoning

The same questions from Phase 1 were submitted again.

### Updated Password Policy

The assistant now responded using the modified reference material.

The response reflected:

- 180-day password rotation
- 8-character minimum passwords
- Removal of complexity requirements
- External password reset portal
- Email-only authentication

These values matched the newly ingested reference material.

---

### Deployment Process

The deployment workflow remained unchanged.

Only the password policy had been modified.

---

## Security Analysis

This exercise demonstrated that:

- The language model itself was unchanged.
- No prompts were modified.
- No model weights were retrained.
- The assistant simply relied on the newly ingested reference material.

The behavioural change resulted entirely from modifications made to the trusted knowledge source.

This illustrates how data poisoning targets the information an AI system retrieves rather than altering the model directly.

---

## Key Findings

The practical demonstration showed that:

- Trusted reference material directly influences AI responses.
- Modifying the knowledge base can immediately alter system behaviour.
- Retrieval-Augmented Generation (RAG) systems inherit the integrity of their underlying data sources.
- Weak validation during knowledge ingestion allows poisoned information to become authoritative.
- Behavioural changes can occur without modifying prompts, application code, or model parameters.

---

# Security Lessons Learned

Throughout this project, I examined how attackers influence AI behaviour by targeting trusted data rather than the model itself.

The investigation covered:

- Training data poisoning
- Embedding-level poisoning
- Corpus poisoning
- Ingestion pipeline attacks
- Behavioural drift
- Layered detection and mitigation strategies
- Practical poisoning of a RAG knowledge base

I also explored defensive practices including:

- Source validation
- Controlled ingestion workflows
- Behavioural monitoring
- Governance of trusted knowledge sources
- Layered security controls across AI data pipelines

---

# Outcome

This project strengthened my understanding of how data poisoning compromises Retrieval-Augmented Generation (RAG) systems by manipulating the information AI models learn from or retrieve.

Through the study of training data poisoning, embedding manipulation, corpus flooding, ingestion pipeline abuse, and behavioural drift, I gained practical insight into how attackers can influence AI outputs without modifying prompts or model weights.

The practical demonstration reinforced the importance of validating trusted knowledge sources, securing ingestion pipelines, and continuously monitoring AI behaviour to preserve the integrity of enterprise AI systems.

---

## Disclaimer

> **Authorized Training Environment**
>
> All activities documented in this project were performed within an authorized cybersecurity training laboratory and controlled learning environment. The demonstrations are intended solely for defensive security education, AI security research, and secure AI system development.
> 
