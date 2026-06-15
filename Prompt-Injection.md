# 🎯 Prompt Injection in Large Language Models

## Overview

Prompt Injection is one of the most critical security risks affecting Large Language Model (LLM) applications. It occurs when an attacker manipulates a model's behavior by embedding malicious instructions within data processed by the model.

Unlike traditional software vulnerabilities that exploit code execution flaws, prompt injection targets the model's instruction-following behavior, causing it to ignore intended restrictions and perform unintended actions.

Prompt Injection is ranked as a major risk within the OWASP LLM Top 10 and is a primary concern for organizations deploying AI-powered systems.

---

# Understanding How LLMs Process Instructions

LLMs generate responses using a **context window**, which contains all information available during inference.

Typical context sources include:

- System prompts
- Developer prompts
- User prompts
- Retrieved documents (RAG)
- Tool outputs

Although these inputs are logically separated, the model ultimately processes them as a single sequence of tokens.

This creates a fundamental security challenge:

> The model does not truly understand authority; it predicts the most likely next token based on the entire context it receives.

Because of this, carefully crafted instructions can influence model behavior even when they conflict with developer-defined rules.

---

# Why Prompt Injection Exists

Most modern LLM applications attempt to separate instructions using mechanisms such as:

- Role-based message structures
- System prompts
- ChatML formatting
- Instruction hierarchies
- Input filtering

However, these boundaries are implemented through prompt design and model training rather than strict architectural isolation.

As a result, malicious instructions may be interpreted as legitimate directives.

This ambiguity forms the foundation of prompt injection attacks.

---

# Direct Prompt Injection

Direct Prompt Injection occurs when an attacker provides malicious instructions directly through the user interface.

### Example

A translation assistant is instructed to:

> Translate English text into Spanish.

Instead of providing text to translate, an attacker submits instructions designed to override the original task.

The model may prioritize the malicious instruction and abandon its intended function.

---

## Security Impact

Direct prompt injection can lead to:

- System prompt disclosure
- Safety policy bypass
- Confidential data exposure
- Unauthorized tool execution
- Manipulation of business workflows

---

# Common Prompt Injection Techniques

## Instruction Override

Attackers attempt to replace or supersede existing instructions.

Examples include:

- Ignore previous instructions
- Disregard earlier directives
- Follow these new rules instead

---

## Synonym-Based Injection

Attackers rephrase known override phrases to bypass simple filtering mechanisms.

Examples:

- Disregard prior guidance
- Replace previous directives
- Override existing instructions

---

## Format-Based Injection

Malicious instructions are hidden inside:

- HTML comments
- Markdown
- YAML metadata
- Source code comments
- Structured documents

The content appears harmless to users but is interpreted by the model during processing.

---

## Simulated Dialogue Injection

Attackers create fake conversation histories to influence model behavior.

The model may interpret the fabricated interaction as legitimate context and continue the conversation in the attacker's intended direction.

---

## Multi-Turn Prompt Shaping

Instead of attacking in a single request, instructions are gradually introduced across multiple interactions.

These instructions remain in conversation history and can later be activated through trigger phrases or follow-up prompts.

---

# Real-World Prompt Injection Incidents

## Bing Chat ("Sydney") Prompt Leak

Researchers successfully manipulated Microsoft's Bing Chat into revealing its hidden system prompt.

The disclosure exposed:

- Internal instructions
- Safety constraints
- Operational behavior
- The codename "Sydney"

### Security Impact

Demonstrated the risk of system prompt leakage and instruction hierarchy failure.

---

## AI Social Media Bot Manipulation

Researchers demonstrated that AI-powered social media bots could be manipulated into publishing false or inappropriate statements through crafted prompts.

### Security Impact

- Reputational damage
- Brand trust erosion
- Public misinformation

---

## AI-Powered Commerce Manipulation

Prompt injection attacks have successfully manipulated customer service chatbots into making unauthorized commitments and generating invalid purchase agreements.

### Security Impact

- Financial exposure
- Contractual risk
- Business logic abuse

---

# Indirect Prompt Injection

Indirect Prompt Injection is a more advanced attack where malicious instructions are embedded within external content rather than entered directly by the attacker.

The user interacts with legitimate content while the model unknowingly processes attacker-controlled instructions hidden within that content.

---

## Common Attack Vectors

### Web Content

Hidden instructions embedded within:

- HTML comments
- Invisible text
- Metadata
- Page content

---

### Emails and Documents

Malicious instructions concealed within:

- Email bodies
- PDFs
- Word documents
- Shared files

---

### AI Agents and Tool Integrations

Attackers hide instructions inside:

- Documentation
- Configuration files
- Repository content
- Tool responses

---

### Retrieval-Augmented Generation (RAG)

Malicious content inserted into:

- Knowledge bases
- Vector databases
- Retrieved documents

When retrieved during inference, the injected instructions become part of the model's context window.

---

# Security Risks of Indirect Prompt Injection

Indirect prompt injection can result in:

- Unauthorized actions
- Sensitive data disclosure
- Tool abuse
- Business process manipulation
- Malicious output generation
- Zero-click exploitation

These attacks are especially dangerous because users may never interact directly with the attacker.

---

# Detection Opportunities

Security teams should monitor for:

- Unusual model outputs
- Prompt leakage attempts
- Unexpected tool execution
- Context manipulation indicators
- Sudden behavioral deviations
- Retrieval source anomalies

---

# Mitigation Strategies

## Input Validation

Treat all external content as untrusted input.

---

## Context Separation

Maintain strong boundaries between:

- System instructions
- User input
- Retrieved content
- Tool outputs

---

## Least Privilege for AI Tools

Limit model access to:

- Databases
- APIs
- Email systems
- File systems

---

## Output Monitoring

Validate responses before allowing downstream actions.

---

## RAG Content Security

Implement:

- Content validation
- Source verification
- Data provenance controls

---

# Framework Mapping

## OWASP LLM Top 10

- LLM01: Prompt Injection
- LLM06: Excessive Agency
- LLM07: System Prompt Leakage

## MITRE ATLAS

- AML.T0051 — LLM Prompt Injection
- AML.T0024 — Model Extraction
- AML.T0015 — Evade ML Model

---

# Key Takeaways

Prompt Injection is fundamentally a trust-boundary problem.

Because LLMs process multiple instruction sources within a shared context window, attackers can manipulate model behavior through carefully crafted inputs.

Effective defenses require treating all external content as untrusted, enforcing strict separation between instruction sources, and limiting the actions AI systems can perform when compromised.
