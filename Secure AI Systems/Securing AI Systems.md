# Securing AI Systems

> Performed a security architecture assessment of an enterprise AI-powered code review assistant by mapping AI system components, identifying trust boundaries, analyzing AI-specific attack surfaces, and applying secure design principles to reduce architectural risk before production deployment.

---

# Overview

Modern AI systems introduce security challenges that extend far beyond traditional web applications. Unlike conventional applications that process structured requests through predictable workflows, AI-powered systems accept free-form natural language, retrieve external context, invoke internal tools, and generate dynamic responses that can directly influence downstream systems.

In this project, I performed a security architecture assessment of **TryAssist**, an AI-powered code review assistant developed by TryTrainMe. The assessment focused on understanding how AI components expand the attack surface, identifying trust boundaries throughout the application architecture, mapping system-level threats using the **OWASP LLM Top 10 (2025)** and **MITRE ATLAS**, and evaluating secure design patterns capable of reducing architectural risk before production deployment.

The project concluded with a practical security audit of the AI assistant, identifying architectural weaknesses, excessive permissions, information disclosure risks, and opportunities to strengthen the overall security posture prior to deployment.

---

# Objectives

- Understand how AI systems differ from traditional application architectures
- Identify the core components of a production AI system
- Map trust boundaries across an AI application
- Analyze AI-specific attack surfaces
- Apply the OWASP LLM Top 10 (2025) to architectural security reviews
- Understand MITRE ATLAS as an AI threat framework
- Evaluate secure AI design patterns
- Perform a pre-deployment security assessment of an enterprise AI assistant

---

# Skills Learned

- AI Security Architecture
- AI Attack Surface Analysis
- Trust Boundary Assessment
- AI Threat Modeling
- Secure AI System Design
- OWASP LLM Top 10 (2025)
- MITRE ATLAS Framework
- NIST AI Risk Management Framework
- Secure AI Auditing
- AI Security Governance

---

# Technologies & Concepts

- Large Language Models (LLMs)
- Retrieval-Augmented Generation (RAG)
- Vector Stores
- Prompt Construction
- AI Orchestration Layers
- API Gateway
- Tool Invocation
- OWASP LLM Top 10 (2025)
- MITRE ATLAS
- NIST AI RMF
- MLSecOps

---

# Framework Alignment

| Framework | Purpose |
|----------|---------|
| OWASP LLM Top 10 (2025) | AI application vulnerability classification |
| MITRE ATLAS | Adversary tactics and techniques against AI systems |
| NIST AI RMF | Organizational AI risk management and governance |

---

# Project Walkthrough

---

# AI System Architecture Assessment

## Objective

Understand how integrating AI into an enterprise application changes the overall system architecture and introduces new components that expand the application's attack surface.

---

### Activities Performed

- Compared traditional and AI-augmented application architectures
- Identified AI-specific architectural components
- Investigated AI data processing workflows
- Examined how AI changes application trust relationships
- Evaluated architectural differences between deterministic software and AI-driven systems

---

## Traditional vs AI-Augmented Architecture

Traditional applications follow predictable workflows where requests move between the user interface, application logic, APIs, and databases. Security controls are typically positioned around these well-defined communication paths.

Introducing an AI component fundamentally changes this architecture by adding new processing layers that traditional security controls were not designed to monitor.

---

## Architectural Comparison

| Component | Traditional Application | AI-Augmented Application |
|-----------|------------------------|--------------------------|
| User Input | Structured forms and API parameters | Free-form natural language |
| Processing | Deterministic application logic | Probabilistic LLM inference |
| Data Access | Direct database queries | Retrieval-Augmented Generation (RAG) |
| Output | Template-rendered responses | AI-generated natural language |
| Dependencies | Libraries and frameworks | Libraries, pretrained models, embeddings |

---

## Security Assessment

The investigation showed that AI introduces several architectural components that significantly expand the application's attack surface.

Unlike traditional software that processes structured data, AI systems must interpret unrestricted natural language while interacting with multiple supporting services such as vector databases, orchestration layers, and external models.

These additional components introduce entirely new trust relationships that require dedicated security controls.

---

### Security Findings

The assessment identified several architectural changes introduced by AI integration:

- Free-form natural language replaces structured user input.
- AI-generated responses replace deterministic application output.
- Retrieval mechanisms introduce external context into model prompts.
- Additional orchestration layers coordinate AI workflows.
- AI systems rely on significantly more interconnected components than traditional applications.

---

# Key Findings

- AI fundamentally changes application architecture.
- New AI components introduce additional attack surfaces.
- Existing web application security controls alone are insufficient to protect AI-powered systems.

---


# AI System Component Assessment

## Objective

Identify the core components that make up a production AI system and evaluate how each contributes to the overall security posture of the application.

---

### Activities Performed

- Examined the architecture of the TryAssist AI system
- Identified every major AI component within the production environment
- Investigated the responsibility of each component
- Analyzed how data is processed throughout the application
- Evaluated the security significance of each architectural layer

---

## AI System Architecture

Unlike traditional applications that primarily consist of a user interface, application logic, APIs, and databases, AI-powered applications introduce additional components responsible for orchestrating model interactions, retrieving contextual information, invoking external tools, and monitoring AI operations.

The TryAssist architecture consists of nine primary components, each responsible for processing data at different stages of a user request.

---

## Architecture Components

| Component | Function |
|-----------|----------|
| User Interface | Developer-facing chat interface used to communicate with TryAssist |
| API Gateway | Authenticates requests, applies rate limiting, and routes traffic |
| Orchestration Layer | Coordinates conversations and manages request flow between AI components |
| Prompt Construction | Combines system instructions, user input, and retrieved context into the final prompt |
| Large Language Model (LLM) | Processes prompts and generates natural language responses |
| Tool Layer | Enables the LLM to interact with databases, APIs, documentation, and CI/CD resources |
| Output Processing | Formats responses and applies filtering before delivery |
| Logging & Monitoring | Records conversations, usage analytics, and audit events |
| Vector Store | Stores embedded internal documentation for Retrieval-Augmented Generation (RAG) |

---

## Architecture Assessment

The assessment revealed that each architectural component performs a specialized function within the AI workflow.

Unlike conventional applications where application logic directly communicates with backend services, AI systems introduce multiple intermediary layers before a response reaches the user.

These additional layers increase operational complexity while simultaneously expanding the number of components that require dedicated security controls.


### Security Findings

The architectural review identified several AI-specific components that do not exist in traditional applications, including:

- Prompt construction pipelines
- Large Language Models
- Tool invocation mechanisms
- Vector stores supporting Retrieval-Augmented Generation
- AI-specific monitoring and conversation logging

Each component introduces additional opportunities for unauthorized access, misuse, or unintended information disclosure if appropriate controls are not implemented.

---

# Key Findings

- AI systems consist of significantly more components than traditional applications.
- Every architectural component introduces its own security considerations.
- AI orchestration and retrieval mechanisms require dedicated security review before deployment.

---

# Trust Boundary Analysis

## Objective

Identify trust boundaries within the TryAssist architecture and evaluate how data transitions between different security contexts throughout the AI workflow.

---

### Activities Performed

- Identified trust boundaries across the AI architecture
- Traced the flow of user requests through the system
- Evaluated security context transitions
- Investigated where untrusted data enters trusted environments
- Assessed how information moves between AI components

---

## Trust Boundary Assessment

A trust boundary represents the point where information moves from one security context into another.

Every transition between trusted and untrusted components creates an opportunity for malicious input, unauthorized actions, or sensitive data exposure.

The TryAssist architecture contains five primary trust boundaries.

---

## Identified Trust Boundaries

| Trust Boundary | Data Crossing |
|----------------|---------------|
| User-to-System | Untrusted natural language submitted by developers |
| System-to-LLM | Constructed prompts containing system instructions, user input, and retrieved context |
| LLM-to-Tools | AI-generated output triggering tool execution, database queries, or API requests |
| System-to-External Data | Documentation retrieved from vector stores or external sources |
| System-to-User | AI-generated responses delivered back to the user |

---

## Data Flow Assessment

To understand how trust boundaries interact, a single request was traced through the TryAssist architecture.

### Request Flow

1. A developer submits a natural language request through the chat interface.
2. The API Gateway authenticates the request and applies rate limiting.
3. The Orchestration Layer retrieves conversation history and coordinates processing.
4. Prompt Construction combines:
   - System instructions
   - User input
   - Retrieved documentation from the Vector Store
5. The completed prompt is submitted to the Large Language Model.
6. The model determines whether additional tools must be invoked.
7. Requested tools retrieve the required information and return the results.
8. The model generates a final response.
9. Output Processing applies formatting and filtering.
10. The completed response is returned to the user while the entire interaction is recorded by the logging system.

---

### Security Assessment

Every stage within the request lifecycle crosses at least one trust boundary.

Each transition introduces the possibility of malicious input, unauthorized tool execution, sensitive data exposure, or manipulation of AI-generated responses.

Without security controls at every boundary, a compromise at one stage can affect downstream components throughout the application.



### Security Findings

The trust boundary assessment demonstrated that AI systems process information through multiple security contexts before producing a response.

Unlike traditional applications, AI workflows repeatedly transition between trusted and untrusted environments, requiring layered security controls across every stage of the request lifecycle.

---

# Key Findings

- Every AI request crosses multiple trust boundaries.
- Each boundary represents a potential attack surface.
- AI systems require security controls at every transition between components.
- Mapping trust boundaries provides the foundation for identifying architectural risks before deployment.

---


# AI Attack Surface Assessment

## Objective

Identify the primary attack surfaces introduced by AI-powered applications and map architectural risks using industry-recognized AI security frameworks.

---

### Activities Performed

- Identified AI-specific attack surfaces within the TryAssist architecture
- Reviewed the OWASP LLM Top 10 (2025) framework
- Examined MITRE ATLAS as an AI adversary knowledge base
- Investigated the NIST AI Risk Management Framework (AI RMF)
- Mapped architectural risks to established AI security frameworks

---

## AI Attack Surface Analysis

Traditional security assessments primarily focus on web applications, APIs, authentication systems, databases, and network infrastructure. AI-powered systems introduce additional attack surfaces that extend beyond conventional application security.

As TryAssist processes natural language, retrieves contextual information, invokes internal tools, and interacts with external AI components, every additional capability expands the overall attack surface.

To systematically classify these risks, the assessment referenced three widely adopted AI security frameworks.

---

## AI Security Frameworks

| Framework | Purpose |
|----------|---------|
| **OWASP LLM Top 10 (2025)** | Identifies the most critical security risks affecting Large Language Model applications |
| **MITRE ATLAS** | Documents adversary tactics and techniques targeting AI and machine learning systems |
| **NIST AI Risk Management Framework (AI RMF)** | Provides governance and risk management guidance throughout the AI lifecycle |

These frameworks provide a structured methodology for identifying, classifying, and mitigating risks introduced by AI-powered systems.

---

# OWASP LLM Top 10 (2025) Assessment

## Objective

Identify the architectural risks within TryAssist using the OWASP LLM Top 10 (2025) framework.

---

### Activities Performed

- Reviewed architectural risks affecting AI applications
- Identified system-level vulnerabilities
- Distinguished architectural risks from model-specific attacks
- Mapped AI security concerns to OWASP classifications

---

## System-Level Risk Categories

The architectural review focused on the five OWASP LLM Top 10 (2025) categories that directly affect AI system design rather than the internal behavior of the language model.

| OWASP Category | Description |
|---------------|-------------|
| **LLM02** | Sensitive Information Disclosure |
| **LLM05** | Improper Output Handling |
| **LLM06** | Excessive Agency |
| **LLM07** | System Prompt Leakage |
| **LLM10** | Unbounded Consumption |

These risks originate from architectural decisions, system integrations, and trust relationships established during application design.

---

### Security Findings

Unlike model-specific attacks, these vulnerabilities are introduced through the way AI components interact with users, external services, tools, and downstream systems.

Addressing these risks requires architectural controls rather than changes to the language model itself.

---

# Key Findings

- AI system architecture directly influences application security.
- Several OWASP LLM risks originate from system design rather than model behavior.
- Architectural reviews should identify these risks before deployment.

---

# MITRE ATLAS Assessment

## Objective

Understand how adversaries target AI systems by mapping attacks to the MITRE ATLAS framework.

---

### Activities Performed

- Reviewed the MITRE ATLAS framework
- Examined the AI attack lifecycle
- Investigated adversary tactics against AI systems
- Evaluated how attackers progress through AI environments

---

## Adversary Lifecycle

MITRE ATLAS provides a structured knowledge base describing how attackers compromise AI systems.

Rather than classifying vulnerabilities, ATLAS focuses on the progression of an adversary through an AI environment.

The assessment identified the following attack progression:

| Attack Stage | Description |
|-------------|-------------|
| Reconnaissance | Gathering information about the AI system and its exposed components |
| Initial Access | Compromising an entry point into the AI environment |
| Execution | Executing attacks such as prompt injection or adversarial inputs |
| Persistence | Maintaining long-term access through techniques such as backdoored model weights |
| Impact | Achieving objectives including data theft, manipulation, or service disruption |

---

### Security Findings

MITRE ATLAS complements OWASP by describing how attackers exploit AI systems after identifying vulnerable components.

Using both frameworks provides defenders with a better understanding of both architectural weaknesses and adversary behavior.

---

# Key Findings

- OWASP identifies AI vulnerabilities.
- MITRE ATLAS explains how attackers exploit them.
- Combining both frameworks improves AI threat analysis.

---

# NIST AI Risk Management Framework Assessment

## Objective

Examine how organizational governance supports secure AI deployment throughout the AI lifecycle.

---

### Activities Performed

- Reviewed the NIST AI Risk Management Framework
- Examined AI governance principles
- Identified the four core AI RMF functions
- Evaluated organizational approaches to AI risk management

---

## AI Risk Management Functions

Unlike OWASP and MITRE ATLAS, which focus on vulnerabilities and adversary techniques, the NIST AI RMF provides a governance framework for managing AI risk throughout an organization's operations.

The framework consists of four primary functions.

| Function | Purpose |
|----------|---------|
| **Govern** | Establish AI governance policies and accountability |
| **Map** | Identify AI systems and understand associated risks |
| **Measure** | Assess and monitor AI-related risks |
| **Manage** | Respond to and mitigate identified risks |

---

### Security Findings

The NIST AI RMF emphasizes that AI security extends beyond technical controls.

Effective AI security also requires governance, continuous risk assessment, and structured organizational processes throughout the AI lifecycle.

---

# Key Findings

- AI security requires both technical and organizational controls.
- Governance plays a critical role in secure AI deployment.
- The NIST AI RMF complements OWASP and MITRE ATLAS by focusing on long-term AI risk management.

---


# System-Level Threat Assessment

## Objective

Evaluate the primary system-level threats affecting AI-powered applications by assessing how architectural design decisions introduce security risks before deployment.

---

### Activities Performed

- Reviewed the five OWASP LLM Top 10 (2025) system-level threat categories covered in the assessment
- Analyzed how each threat affects the TryAssist architecture
- Examined real-world scenarios demonstrating each architectural weakness
- Identified the impact of each threat on the Confidentiality, Integrity, and Availability (CIA) triad
- Evaluated defensive measures recommended during the architectural review

---

## LLM10 – Unbounded Consumption

### Assessment

Unbounded Consumption occurs when attackers deliberately increase resource usage or operational costs by submitting excessive numbers of requests or extremely large inputs to an AI system.

Because LLMs consume computational resources based on request volume and input size, poorly protected AI applications can experience significant increases in operational cost or reduced service availability.

---

### TryAssist Scenario

The assessment demonstrated a scenario where an automated script continuously submits hundreds of requests per minute, each containing an extremely large codebase for analysis.

Without controls at the API Gateway, resource consumption increases rapidly.

---

### Recommended Controls

- Rate limiting
- Input length validation
- Cost ceilings
- Per-user request quotas

---

### Security Findings

The investigation showed that resource exhaustion is not limited to traditional denial-of-service attacks. AI systems also introduce financial risks through excessive model usage and token consumption.

---

# Key Findings

- AI systems require controls that limit both request volume and input size.
- API Gateways play an important role in preventing excessive resource consumption.

---

# LLM07 – System Prompt Leakage

## Objective

Assess the risk of exposing internal AI operating instructions through system prompt disclosure.

---

### Activities Performed

- Reviewed the purpose of system prompts
- Examined information commonly stored within system prompts
- Investigated prompt extraction scenarios
- Evaluated the architectural impact of prompt disclosure

---

## Assessment

System prompts define how an AI assistant behaves by providing operational instructions before user input is processed.

Within TryAssist, these instructions include behavioural guidance, internal tool information, response restrictions, and operational rules.

If exposed, attackers gain valuable insight into how the AI system operates.

---

### TryAssist Scenario

The architectural review identified a scenario where the system prompt contains:

- Internal CI/CD API addresses
- Database schema information
- Behavioural instructions

If extracted, an attacker gains internal architectural information without directly accessing the network.

---

### Recommended Controls

- Do not store secrets within system prompts.
- Avoid embedding credentials or internal URLs.
- Design prompts assuming they may eventually become visible.

---

### Security Findings

The assessment demonstrated that system prompts should never be treated as confidential storage locations.

Sensitive operational information increases the impact of prompt disclosure.

---

# Key Findings

- System prompts should contain operational guidance rather than sensitive information.
- Internal architecture details should never be embedded within prompts.

---

# LLM05 – Improper Output Handling

## Objective

Assess the risks introduced when AI-generated output is trusted by downstream systems without validation.

---

### Activities Performed

- Reviewed AI output processing workflows
- Examined downstream execution risks
- Investigated SQL injection scenarios involving AI-generated content
- Evaluated defensive validation techniques

---

## Assessment

Improper Output Handling occurs when AI-generated responses are forwarded directly into downstream systems without validation.

Although LLMs generate text, that text may later be interpreted as SQL queries, shell commands, or HTML if appropriate safeguards are not implemented.

---

### TryAssist Scenario

The assessment examined a scenario where a developer submits a pull request containing:

```text
'; DROP TABLE users; --
```

If that AI-generated content is later incorporated directly into a database query without parameterization, the downstream system becomes vulnerable.

---

### Recommended Controls

- Never trust LLM-generated output.
- Parameterize database queries.
- Avoid constructing SQL, shell commands, or HTML directly from AI responses.

---

### Security Findings

The investigation demonstrated that AI output should always be treated as untrusted input before interacting with downstream applications.

---

# Key Findings

- AI-generated content requires validation before execution.
- Output validation is an essential architectural security control.

---

# LLM06 – Excessive Agency

## Objective

Evaluate whether AI components possess more capabilities, permissions, or autonomy than required for their intended purpose.

---

### Activities Performed

- Reviewed AI tool permissions
- Examined system autonomy
- Investigated privilege allocation
- Assessed excessive functionality within the TryAssist architecture

---

## Assessment

The architectural review identified three forms of excessive agency:

| Type | Description |
|------|-------------|
| Excessive Functionality | AI can access tools beyond its intended purpose |
| Excessive Permissions | Tools possess more privileges than required |
| Excessive Autonomy | AI performs actions without human approval |

---

### TryAssist Scenario

The assessment identified that the database tool possesses **UPDATE** and **DELETE** capabilities despite functioning as a code review assistant.

This creates unnecessary risk if AI-generated actions are manipulated.

---

### Recommended Controls

- Apply Least Privilege
- Use scoped API tokens
- Require human approval before write, delete, or deployment operations

---

### Security Findings

The investigation demonstrated that AI systems should receive only the permissions required to perform their assigned functions.

---

# Key Findings

- AI tools should operate using least privilege.
- Human approval should protect high-impact operations.

---

# LLM02 – Sensitive Information Disclosure

## Objective

Assess how AI systems may expose confidential information through user interactions, logging, or system behavior.

---

### Activities Performed

- Examined AI conversation logging
- Reviewed sensitive information exposure risks
- Investigated data retention practices
- Evaluated secure storage considerations

---

## Assessment

AI systems routinely process highly sensitive information including source code, credentials, environment variables, and internal documentation.

Without appropriate safeguards, this information may be retained within conversation logs or exposed through AI responses.

---

### TryAssist Scenario

The assessment reviewed a scenario where a developer accidentally pastes a private SSH key into the AI assistant during a code review.

The entire conversation, including the key, is stored without filtering.

---

### Recommended Controls

- Remove PII before storage.
- Encrypt conversation logs.
- Carefully evaluate information sent to external AI providers.

---

### Security Findings

The investigation demonstrated that sensitive information disclosure can occur without exploiting a software vulnerability.

Information may simply be retained exactly as users submit it.

---

# Key Findings

- AI conversation logs require the same level of protection as other sensitive organizational data.
- Sensitive information should be filtered before long-term storage.

---

## CIA Impact Assessment

The architectural review mapped each system-level threat to the security objective primarily affected.

| Threat | CIA Impact |
|---------|------------|
| LLM10 – Unbounded Consumption | Availability |
| LLM07 – System Prompt Leakage | Confidentiality |
| LLM05 – Improper Output Handling | Integrity |
| LLM06 – Excessive Agency | Integrity, Availability |
| LLM02 – Sensitive Information Disclosure | Confidentiality |

---



# Secure AI System Design Review

## Objective

Evaluate the security controls implemented across the TryAssist architecture and assess how secure design principles reduce AI-specific risks before production deployment.

---

### Activities Performed

- Reviewed defense-in-depth strategies for AI systems
- Assessed security controls at each trust boundary
- Evaluated the principle of least privilege for AI components
- Investigated input and output validation techniques
- Examined monitoring and observability practices throughout the AI lifecycle
- Reviewed the role of MLSecOps in securing AI systems

---

## Defense in Depth Assessment

AI systems process information across multiple trust boundaries, making a single security control insufficient to protect the entire architecture.

The assessment applied a defense-in-depth approach by placing security controls at every trust boundary identified during the architectural review.

---

## Security Controls by Trust Boundary

| Trust Boundary | Security Controls |
|----------------|-------------------|
| **User-to-System** | Input length validation, rate limiting, content filtering, authentication |
| **System-to-LLM** | Prompt injection detection, system prompt hardening, context size limits |
| **LLM-to-Tools** | Parameterized queries, least-privilege permissions, approval workflows |
| **System-to-External Data** | Source validation, document verification, content sanitization |
| **System-to-User** | Output sanitization, PII redaction, response length limits, content safety filtering |

---

### Security Assessment

Rather than relying on a single defensive mechanism, multiple layers of security protect each stage of the AI request lifecycle.

If one security control fails, additional controls remain in place to reduce the likelihood of a successful attack.

For example, a prompt injection attempt that bypasses initial input filtering may still be prevented from causing harm because the tool layer requires human approval before executing privileged operations.

---

### Security Findings

The architectural review demonstrated that AI security depends on applying controls throughout the entire request lifecycle rather than protecting only the language model itself.

Each trust boundary requires its own dedicated security controls.

---

# Key Findings

- AI security should be implemented at every trust boundary.
- Multiple defensive layers reduce the likelihood of successful attacks.
- Individual security controls should complement one another rather than operate independently.

---

# Least Privilege Assessment

## Objective

Evaluate whether AI components operate with only the permissions required to perform their intended functions.

---

### Activities Performed

- Reviewed permissions assigned to AI tools
- Examined API access controls
- Evaluated database permissions
- Investigated tool allowlisting
- Assessed human approval requirements for privileged operations

---

## Least Privilege Review

Every AI component should receive only the minimum permissions necessary to complete its assigned task.

Applying least privilege limits the impact of compromised AI components and reduces opportunities for unauthorized actions.

The assessment identified several recommended security practices.

| Component | Recommended Control |
|-----------|---------------------|
| Database Access | Read-only by default |
| API Tokens | Scoped to required endpoints only |
| Tool Access | Allowlist approved functions |
| State-Changing Operations | Require human approval before execution |

---

### Security Findings

The review demonstrated that restricting permissions reduces the impact of excessive agency and limits the damage that could result from compromised AI components.

---

# Key Findings

- AI components should operate with minimum required permissions.
- Administrative privileges should not be granted unless operationally necessary.
- Human approval provides an additional safeguard for high-impact actions.

---

# Input and Output Validation Assessment

## Objective

Assess how validation controls protect AI systems from malicious input and unsafe AI-generated output.

---

### Activities Performed

- Reviewed input validation strategies
- Examined output validation controls
- Investigated structured output generation
- Evaluated downstream execution risks

---

## Input Validation

Unlike traditional applications that receive structured input, AI systems process unrestricted natural language.

The assessment identified several controls that reduce risks before requests reach the orchestration layer.

These include:

- Input length validation
- Detection of known prompt injection patterns
- Request filtering before AI processing

---

## Output Validation

AI-generated responses should never be trusted automatically.

Before output reaches downstream systems, only the expected structured data should be extracted while unnecessary or unexpected content is discarded.

Where possible, AI responses should be constrained to predefined schemas to reduce opportunities for unintended execution.

---

### Security Findings

The assessment demonstrated that both input validation and output validation remain essential security controls, even though AI systems process natural language instead of structured requests.

---

# Key Findings

- AI input should be validated before reaching the language model.
- AI-generated output should be validated before interacting with downstream systems.
- Structured output reduces the overall attack surface.

---

# Monitoring and Observability Assessment

## Objective

Evaluate monitoring practices that improve visibility into AI system behavior and assist with detecting security incidents.

---

### Activities Performed

- Reviewed AI monitoring requirements
- Examined operational metrics
- Investigated security logging practices
- Evaluated MLSecOps throughout the AI lifecycle

---

## AI Monitoring Review

Traditional monitoring focuses on infrastructure, applications, and network activity.

AI-powered applications introduce additional operational characteristics that require continuous observation.

The assessment identified several important monitoring areas.

| Monitor | Purpose |
|----------|---------|
| Request Patterns | Detect abnormal usage and automated probing |
| Token Consumption | Identify excessive resource usage and cost spikes |
| Tool Invocations | Detect unexpected or unauthorized tool execution |
| Response Anomalies | Identify unusual changes in AI-generated responses |
| System Prompt Extraction Attempts | Detect attempts to expose internal operating instructions |
| Cost Metrics | Monitor operational spending and trigger budget alerts |

---

## MLSecOps Assessment

The review also examined **MLSecOps**, the practice of integrating security throughout the machine learning lifecycle.

Rather than introducing security controls after deployment, MLSecOps applies security during development, testing, deployment, and ongoing operations.

This approach supports continuous monitoring, incident response, and secure operation of AI systems.

---

### Security Findings

The assessment demonstrated that monitoring complements preventive security controls by identifying attacks that bypass existing defenses.

Continuous visibility across AI operations strengthens the organization's ability to detect and respond to emerging threats.

---

# Key Findings

- Monitoring provides visibility into AI-specific security events.
- AI systems require metrics beyond those used in traditional applications.
- MLSecOps integrates security throughout the machine learning lifecycle rather than after deployment.
