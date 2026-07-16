# AI Models & Data

## Overview

This documentation explores how AI models are fundamentally shaped by the data used during training and examines the security implications introduced throughout the AI data supply chain. It covers training data sources, data provenance, personally identifiable information (PII) exposure, model-building concepts, fine-tuning inheritance risks, and the transparency challenges associated with modern AI models.

---

# Introduction

Every AI model is fundamentally a product of its training data. Before a model generates predictions or responds to prompts, decisions have already been made about:

- What data is collected
- Where the data originates
- How the data is processed

These decisions directly influence model behaviour and introduce security risks that often remain invisible to organisations deploying AI systems.

Training datasets may include publicly scraped information, proprietary organisational data, synthetic datasets, or licensed content. Without proper governance, sensitive information, credentials, and personally identifiable information (PII) may become permanently embedded within trained model weights.

Understanding the AI data supply chain is essential for evaluating model trustworthiness and identifying security risks before deployment.

---

# Learning Objectives

- Explain where AI training data originates and the security risks introduced by poor data provenance.
- Recognise how personally identifiable information (PII) and sensitive credentials become embedded within model weights.
- Understand security implications introduced by overfitting, quantisation, and federated learning.
- Explain the inheritance risks associated with fine-tuning pre-trained models.
- Understand why trained models operate as black boxes and the role of Model Cards in documenting model characteristics.

---

# Training Data

Every AI model depends on its training data.

Before training begins, data must first be collected, processed, and prepared. The origin and quality of this data directly influence both model performance and model security.

Large language models require enormous datasets consisting of hundreds of gigabytes or even trillions of tokens. These datasets are typically assembled from multiple sources.

## Common Sources of Training Data

| Source | Description | Trust Profile |
|---------|-------------|---------------|
| Web Scraping | Automated collection of publicly available internet content including news, forums, blogs, and social media. | Low |
| Licensed Datasets | Data obtained through commercial agreements or platform partnerships. | Medium |
| Synthetic Data | AI-generated content used to train additional AI systems. | Variable |
| Internal Corpora | Organisational knowledge bases, support documentation, and proprietary records used during fine-tuning. | Higher |

One of the most widely used public datasets is **Common Crawl**, a large-scale web archive that has contributed to the training of many modern large language models.

Although these datasets are filtered before training, filtering processes differ significantly between organisations, creating uncertainty regarding the quality and safety of the resulting training data.

---

## Data Provenance

Data provenance refers to the ability to answer three fundamental questions about every piece of training data:

1. Where did the data originate?
2. When was it collected?
3. Has it been modified since collection?

Modern AI supply chains often combine hundreds of independent datasets, making it difficult to accurately trace original sources or verify licensing information.

Poor provenance creates uncertainty regarding:

- Dataset ownership
- Licensing compliance
- Data integrity
- Data authenticity
- Security of training data

To improve transparency, the AI industry has introduced the concept of the **Machine Learning Bill of Materials (ML-BOM)**, which documents:

- Dataset sources
- Licensing information
- Personally identifiable information (PII) categories
- Filtering decisions

ML-BOM serves a similar purpose to the Software Bill of Materials (SBOM) used in software supply chain security.

---

## Personally Identifiable Information (PII) in Training Data

Large-scale web scraping frequently collects publicly accessible information that was never intended for AI training.

Examples include:

- Medical records
- Personal email conversations
- Forum discussions
- Political opinions
- Health-related information
- API keys
- Passwords
- Credentials

Once incorporated into training data, sensitive information may become embedded within model weights.

Removing this information after training is extremely difficult because it has already influenced the learned parameters of the model.

This creates long-term confidentiality risks where models may reproduce portions of their training data during inference.

---

## Security Perspective

The security posture of an AI model is directly influenced by the quality and integrity of its training data.

If datasets contain:

- Unverified sources
- Sensitive information
- Poor provenance
- Malicious content
- Inadequate filtering

these characteristics may become permanently reflected within the trained model.

---

# Building the Model

Training an AI model involves more than simply feeding data into an algorithm. Throughout the training process, several design decisions influence how the model learns, how accurately it performs, and how securely it behaves once deployed.

Key concepts such as epochs, overfitting, model validation, post-training optimisation, and federated learning all introduce distinct security considerations that affect the trustworthiness of the final model.

---

## Epochs and Overfitting

An **epoch** represents one complete pass of the training algorithm through the entire training dataset.

Models are typically trained across multiple epochs, allowing them to gradually improve prediction accuracy by repeatedly adjusting their internal parameters.

However, increasing the number of epochs does not always improve model quality.

Training for too long can result in **overfitting**, where the model memorises the training data instead of learning general patterns.

### Characteristics of Overfitting

- Excellent performance on training data
- Poor performance on unseen data
- Reduced ability to generalise
- Increased likelihood of reproducing memorised information

From a security perspective, overfitting increases the possibility that sensitive information contained within the training data may later be reproduced during inference.

---

## Model Validation

To detect overfitting during training, a portion of the dataset is withheld as a **validation set**.

Unlike the training dataset, validation data is never used to update model parameters. Instead, it is periodically used to evaluate how well the model performs on previously unseen information.

If training accuracy continues to improve while validation accuracy stops improving or begins to decline, the model is likely overfitting.

Model validation serves as an important quality control mechanism by identifying problems before deployment.

From a security standpoint, validation helps detect unexpected behaviours, biases, and anomalies that may have been introduced during training.

---

## Post-Training Optimisation

After training is complete, models are frequently optimised to reduce storage requirements and improve deployment performance.

Two common optimisation techniques are pruning and quantisation.

| Technique | Description | Security Consideration |
|-----------|-------------|------------------------|
| Pruning | Removes parameters that contribute little to model predictions, reducing overall model size. | Alters model behaviour after training and may not be fully documented. |
| Quantisation | Reduces the numerical precision of model weights to lower memory and computational requirements. | May weaken safety mechanisms and reduce the effectiveness of security controls developed for full-precision models. |

These optimisation processes are often performed after training by different teams responsible for packaging models for deployment.

As a result, organisations may inherit undocumented behavioural changes alongside performance improvements.

---

## Federated Learning

Traditional machine learning centralises training data within a single environment.

**Federated learning** follows a different approach by distributing training across multiple devices or organisations.

Instead of sending raw data to a central server, each participant trains the model locally and submits only model weight updates for aggregation.

This approach reduces privacy risks because sensitive datasets remain within their original environments.

### Security Benefits

- Raw data never leaves participating organisations.
- Reduces exposure of sensitive information during training.
- Improves privacy for distributed environments.

### Security Considerations

Although federated learning improves privacy, it introduces new integrity risks.

Participants can intentionally or unintentionally submit manipulated model updates that influence the global model.

Because only weight updates are shared, identifying poisoned or malicious contributions can be significantly more difficult than in centralised training environments.

The security challenge shifts from protecting centralised datasets to protecting the aggregation process itself.

---

## Security Perspective

Every stage of model construction introduces its own security considerations.

- Excessive training can increase memorisation of sensitive information.
- Poor validation may allow unsafe behaviours to remain undetected.
- Post-training optimisation can unintentionally weaken safety mechanisms.
- Federated learning reduces data exposure but increases the complexity of protecting model integrity.

Understanding these concepts provides the foundation for evaluating the security of modern AI models throughout the machine learning lifecycle.
```

The AI data supply chain should therefore be treated as a critical component of AI security, requiring the same level of governance, auditing, and documentation applied to traditional software supply chains.

---# The Inheritance Problem

Training a Large Language Model (LLM) from scratch requires enormous computational resources, vast amounts of training data, and significant financial investment. As a result, most organisations do not build models from the ground up. Instead, they begin with an existing pre-trained model and adapt it for their specific use case.

While this approach significantly reduces development time and cost, it also introduces an important security challenge known as the **inheritance problem**.

---

## Pre-Trained Models

A **pre-trained model** is a model that has already been trained on a large, general-purpose dataset.

These models develop broad language capabilities, including:

- Grammar
- General reasoning
- World knowledge
- Language understanding

Pre-trained models are typically developed by large organisations and distributed through:

- Open model weights
- Hosted APIs

Examples include foundation models that are later adapted for domain-specific tasks.

---

## Fine-Tuning

**Fine-tuning** is the process of continuing the training of a pre-trained model using a smaller, task-specific dataset.

This allows organisations to specialise the model for particular domains or business requirements.

Examples include:

- Healthcare organisations fine-tuning models using clinical documentation.
- Legal firms fine-tuning models using legal case data.
- Enterprise organisations fine-tuning models using internal knowledge bases.

### Fine-Tuning Changes

Fine-tuning modifies:

- Domain-specific knowledge
- Model behaviour
- Response style
- Task-specific capabilities

### Fine-Tuning Does Not Change

Fine-tuning does **not** replace or rebuild the underlying base model.

The original pre-trained weights remain the foundation upon which all new behaviour is built.

---

## The Inheritance Problem

When an organisation fine-tunes a pre-trained model, it also inherits everything already embedded within the original model.

This includes characteristics that may be unknown or impossible to verify.

Inherited characteristics may include:

- Biases introduced during pre-training
- Undocumented behaviours
- Training data artefacts
- Existing vulnerabilities
- Safety alignment limitations

Because organisations rarely have visibility into the original training process, they inherit risks they did not create and may not even be aware exist.

---

## Security Implications

### Safety Alignment Degradation

Safety alignment is not permanent.

Research has shown that even a small amount of additional fine-tuning can gradually weaken previously established safety mechanisms.

Rather than failing immediately, safety controls slowly erode as the model adapts to new datasets.

This increases the likelihood of unsafe responses under certain conditions.

---

### Increased Attack Surface

Fine-tuned models become more specialised for their intended tasks.

While this improves performance within specific domains, it can also reduce resilience against unexpected or adversarial inputs.

Research has demonstrated that fine-tuned models may become more susceptible to prompt injection attacks than their original base models.

Specialisation can therefore increase the overall attack surface.

---

### Version Dependency

Fine-tuning is always performed using a specific version of a pre-trained model.

If vulnerabilities, backdoors, or problematic training data are later discovered within that base model, every derived model inherits those issues.

Without accurate version tracking, organisations cannot reliably determine whether deployed models are affected.

---

## Security Perspective

Deploying a fine-tuned model means deploying the entire pre-trained foundation beneath it.

Organisations inherit:

- The original training data
- The original model architecture
- Embedded behaviours
- Existing biases
- Safety characteristics
- Undocumented risks

Fine-tuning customises model behaviour, but it does not eliminate or replace the characteristics already learned during pre-training.

Understanding the inheritance problem is essential when evaluating the security posture of third-party AI models.
```


---

# The Black Box Problem

Unlike traditional software, trained AI models cannot be inspected to understand exactly how they make decisions.

Software engineers can review source code, analyse binaries, and trace program execution. A trained model, however, consists of billions of numerical parameters that contain no human-readable explanation of how knowledge was learned or why particular decisions are made.

For this reason, AI models are commonly described as **black boxes**.

---

## Models as Black Boxes

A trained model is composed of billions of floating-point values known as **weights**.

These weights represent the cumulative result of the entire training process.

Although they determine how the model behaves, they do not reveal:

- Which training examples influenced a response
- Why specific decisions are made
- How particular behaviours were learned
- What information has been memorised

As a result, organisations cannot inspect a model's internal parameters to fully understand its behaviour.

Instead, trust in a model depends largely on trust in the process used to build it.

---

## Behavioural Testing

Because model internals cannot be directly interpreted, organisations evaluate models by observing their behaviour.

Common evaluation methods include:

- Functional testing
- Benchmarking
- Adversarial prompting
- Red teaming

These approaches provide valuable insight into how a model responds under specific conditions.

However, behavioural testing samples only a small portion of possible inputs.

It cannot guarantee how the model will behave in situations that have not yet been tested.

---

## Model Cards

To improve transparency, AI developers may publish a **Model Card**.

A Model Card is a structured document describing how a model was developed, evaluated, and intended to be used.

Rather than explaining the model's internal weights, it documents important information about the development process and known limitations.

A comprehensive Model Card typically includes:

| Section | Description |
|----------|-------------|
| Training Data | Data sources, filtering methods, and known limitations of the training data. |
| Intended Use | Appropriate use cases and scenarios for which the model was designed. |
| Evaluation Results | Performance metrics measured across different datasets and conditions. |
| Known Limitations | Situations where the model may perform poorly or behave unexpectedly. |
| Bias Assessment | Identified biases introduced through training or evaluation. |
| Licence | Legal terms governing use and distribution of the model. |

Model Cards provide important context that cannot be obtained by inspecting model weights alone.

---

## Documentation Gaps

Although Model Cards improve transparency, they are not mandatory.

Many published models provide:

- Incomplete documentation
- Limited training data information
- Minimal evaluation details
- Missing bias assessments
- Unclear licensing information

Incomplete documentation reduces an organisation's ability to assess the security, reliability, and trustworthiness of a model before deployment.

Without sufficient documentation, important risks may remain unknown.

---

## Security Perspective

The absence of comprehensive documentation should be treated as a security concern.

Sparse or missing Model Cards make it difficult to determine:

- How the model was trained
- What datasets were used
- What limitations are already known
- Which risks have been evaluated
- Whether security testing was performed

Since trained models operate as black boxes, documentation becomes a critical component of AI security and governance.

Without a reliable audit trail, organisations must rely on trust rather than evidence when evaluating third-party models.

---

# Model Audit Exercise

The practical exercise focused on auditing a publicly shared AI model hosted within a simulated model repository.

The objective was to identify security and supply chain risks by reviewing the model's documentation, metadata, licensing information, training sources, and distributed files.

The exercise demonstrates that evaluating third-party AI models extends beyond measuring model performance. Security assessments should also consider provenance, documentation quality, licensing, dataset trustworthiness, and deployment artifacts before integrating models into production environments.

### Security Findings Identified

| Finding | Risk Level |
|----------|------------|
| Publicly available web sources used for training | High |
| Training data sourced from forums and Q&A websites | High |
| Base model version (enterprise-base-v1.1) | Medium |
| Macro-averaged F1 score of 0.91 | Medium |
| Custom licence requiring direct approval | Medium |
| Large serialized model file (`enterprise-classifier-v2.pklLFS`, 268 MB) | Medium |

---

## Security Perspective

Third-party AI models should be treated as software supply chain components.

Before deployment, organisations should evaluate:

- Training data provenance
- Dataset trustworthiness
- Model documentation
- Licensing conditions
- Performance metrics
- Model artifacts
- Distributed files
- Version history

Conducting model audits helps identify potential security, legal, and operational risks before integrating AI systems into production environments.
