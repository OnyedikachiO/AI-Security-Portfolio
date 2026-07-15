# AI Supply Chain Security

> Explored how modern AI systems rely on external models, datasets, machine learning frameworks, and software dependencies, and investigated how these trust relationships create opportunities for supply chain attacks against AI-powered applications.

---

# Overview

Artificial Intelligence systems depend on far more than application code. Every pretrained model, dataset, framework, and supporting package introduced into an AI environment becomes part of its supply chain. While these external resources accelerate AI development, they also expand the organization's trust boundary and introduce additional attack surfaces.

In this project, I explored the fundamentals of AI supply chains by examining how trust relationships are established across AI components, comparing traditional software supply chains with AI ecosystems, identifying the core components that make up an AI supply chain, and understanding how attackers exploit weaknesses throughout the entire chain. I also reviewed real-world supply chain incidents and performed a practical assessment of AI model repositories to identify trust indicators before deployment.

---

# Objectives

- Understand the concept of an AI supply chain
- Compare traditional software and AI supply chains
- Identify the four major components of an AI supply chain
- Understand different AI deployment models
- Analyze the four primary AI supply chain attack layers
- Review real-world AI supply chain incidents
- Evaluate AI model repositories before deployment

---

# Skills Learned

- AI Supply Chain Analysis
- AI Trust Relationship Assessment
- AI Component Analysis
- Model Repository Evaluation
- AI Deployment Security
- AI Attack Surface Analysis
- AI Risk Assessment
- Secure AI Model Validation

---

# Technologies & Concepts

- Hugging Face
- PyTorch
- TensorFlow
- Scikit-learn
- Kaggle
- PyPI
- Conda
- GitHub
- SafeTensors
- GGUF
- Pickle Serialization
- Transfer Learning
- LoRA
- OWASP LLM Top 10
- MITRE ATLAS

---

# Framework Alignment

| Framework | Mapping |
|----------|---------|
| OWASP LLM Top 10 | LLM03 – Supply Chain Vulnerabilities |
| MITRE ATLAS | AML.T0010 – ML Supply Chain Compromise |

---

# Project Walkthrough

---

# AI Supply Chain Fundamentals

## Objective

Understand how software supply chains evolved into AI supply chains and investigate why trust relationships introduce security risks throughout modern AI deployments.

---

### Activities Performed

- Examined the concept of a traditional supply chain
- Compared software and AI supply chains
- Investigated transitive dependencies
- Reviewed how trust propagates across software ecosystems
- Analyzed notable software supply chain compromises

---

## Traditional Supply Chain Analysis

A supply chain consists of multiple independent suppliers contributing components that collectively produce a finished product.

Just as constructing a house requires materials obtained from different suppliers, software development depends on numerous third-party components developed and maintained by external organizations.

The overall security of the finished product depends upon the integrity of every component introduced throughout its construction.

---

## Software Supply Chain Investigation

Modern software rarely consists entirely of internally developed code.

Instead, applications commonly depend on numerous third-party packages that are installed from external repositories.

During package installation, additional supporting packages are automatically installed even though they were never explicitly requested.

These automatically installed packages are known as **transitive dependencies**.

Every transitive dependency expands the application's trust boundary by introducing additional external software into the environment.

---

## Why Supply Chain Attacks Are Effective

Unlike conventional attacks that directly target an application, supply chain attacks compromise components that organizations already trust.

Instead of attacking every victim individually, attackers compromise a trusted upstream component before it reaches downstream users.

As a result, organizations unknowingly deploy compromised software obtained through legitimate distribution channels.

---

## Software Supply Chain Incident Review

The following incidents demonstrate how compromising trusted software components can impact thousands of downstream users.

| Incident | Year | Investigation |
|----------|------|---------------|
| SolarWinds | 2020 | Malicious code was introduced into the Orion build process before legitimate software updates were distributed. |
| Log4Shell | 2021 | A critical vulnerability within the Log4j library enabled remote code execution across millions of Java applications. |

---

### Security Findings

The investigation demonstrated that:

- Software supply chains rely heavily on trust.
- Applications inherit every dependency introduced by upstream suppliers.
- A compromise affecting one trusted component can rapidly impact thousands of downstream systems.

---

# Key Findings

- Supply chain attacks target trusted components instead of end users.
- Transitive dependencies silently expand an application's attack surface.
- Trust relationships are inherited throughout the software supply chain.

---

# AI Supply Chain Analysis

## Objective

Investigate how AI introduces additional trust relationships beyond traditional software by incorporating externally developed models, datasets, frameworks, and serialized model weights.

---

### Activities Performed

- Examined how AI extends traditional software supply chains
- Investigated pretrained AI models
- Studied serialized model weights
- Explored transfer learning
- Reviewed fine-tuning concepts
- Investigated LoRA adapters

---

## AI Supply Chains

Unlike traditional software supply chains that primarily distribute source code and software libraries, AI supply chains introduce an entirely new asset: **trained models**.

A trained model consists of:

- Model architecture
- Training dataset
- Training process
- Serialized weights

Each component contributes to the final model while introducing an additional trust relationship that developers cannot always independently verify.

---

## Serialized Model Weights

Serialized weights preserve everything a model learned during training.

Much like a saved game file stores a player's progress, serialized weights capture the knowledge acquired throughout model training.

When these weights are loaded, the model resumes operation using the information stored within them.

Because these files originate from external sources, organizations trust not only the model itself but also the process used to create and distribute it.

---

## Transfer Learning Investigation

Rather than training models from scratch, organizations frequently download existing pretrained models and adapt them for new tasks through **transfer learning**.

The most common approach is **fine-tuning**, where an existing model is further trained using a smaller task-specific dataset.

While this significantly reduces training time, it also means organizations inherit trust relationships established during the model's original development.

---

## LoRA Adapter Assessment

Modern fine-tuning techniques frequently utilize **LoRA adapters**.

Instead of distributing an entirely new model, small adapter files modify the behavior of an existing base model.

This introduces another trust dependency because both the original model and the adapter influence the final behavior of the deployed AI system.

---

### Security Findings

The investigation highlighted several unique characteristics of AI supply chains:

- AI systems depend upon externally trained models.
- Model weights cannot always be independently verified.
- Fine-tuning builds upon existing trust relationships.
- LoRA adapters introduce additional supply chain dependencies.

---

# Key Findings

- AI supply chains extend traditional software supply chains.
- Trust extends beyond software packages into pretrained models.
- Every downloaded model represents an external trust relationship.
- Fine-tuning does not eliminate inherited supply chain risks.

---


# AI Supply Chain Component Assessment

## Objective

Identify the core components that make up an AI supply chain and evaluate how each contributes to the overall trust relationship established before an AI application reaches production.

---

### Activities Performed

- Identified the four primary components of an AI supply chain
- Examined the origin of each component
- Investigated external trust relationships
- Mapped dependencies between AI components
- Reviewed how components interact within a typical AI deployment

---

## AI Supply Chain Components

An AI application depends on multiple external resources before it can perform inference. Unlike traditional software, these components extend beyond source code and include machine learning assets developed by third parties.

The AI supply chain consists of four primary components.

| Component | Description | Common Sources | Examples |
|-----------|-------------|----------------|----------|
| Models | Pretrained model architectures and learned weights | Hugging Face Hub, TensorFlow Hub, PyTorch Hub | `bert-base-uncased`, `gpt2` |
| Datasets | Data used for model training and evaluation | Hugging Face Datasets, Kaggle, Academic Repositories | ImageNet, Common Crawl |
| Frameworks | Machine learning libraries responsible for training and inference | PyPI, Conda, GitHub | PyTorch, TensorFlow, Scikit-learn |
| Dependencies | Supporting software packages required by frameworks | PyPI, npm, conda-forge | NumPy, SciPy, Pillow, tokenizers |

---

## AI Supply Chain Architecture Assessment

A typical AI deployment relies on several interconnected components before a model can generate predictions.

The application depends on a machine learning framework.

The framework depends on supporting packages retrieved from external package repositories.

The model itself depends upon training data and serialized weights produced during the training process.

Each relationship forms another point of trust within the AI supply chain.

As a result, deploying a single AI model requires trusting numerous external contributors, repositories, and software ecosystems.



### Security Findings

The assessment identified multiple trust relationships throughout the AI ecosystem.

Rather than trusting only a downloaded model, organizations also trust:

- The repository hosting the model
- The framework responsible for loading it
- Every supporting dependency installed alongside the framework
- The datasets used during model development

Each component contributes to the overall security of the deployed AI application.

---

# Key Findings

- AI applications rely on four primary external components.
- Every component introduces its own trust relationship.
- A compromise affecting any component can impact downstream AI deployments.
- Organizations inherit trust from every external resource incorporated into an AI system.

---

# AI Deployment Model Assessment

## Objective

Compare the two primary methods organizations use to consume AI models and evaluate the unique supply chain risks associated with each deployment approach.

---

### Activities Performed

- Investigated downloadable AI models
- Examined hosted AI services
- Compared deployment trust boundaries
- Reviewed common AI model file formats
- Studied quantization within local large language models

---

## Downloading AI Models

One common deployment approach involves downloading pretrained model files from public repositories such as Hugging Face and executing them within an organization's own infrastructure.

Under this deployment model, every downloaded file crosses the organization's trust boundary before being loaded into memory.

Responsibility for hosting, inference, and security remains entirely with the organization deploying the model.

---

## Model File Format Investigation

Different model formats introduce different security considerations.

| File Format | Assessment |
|-------------|------------|
| `.pkl` | Uses Python pickle serialization and may execute arbitrary code during loading. |
| `.pt` | Pickle-based format capable of executing serialized objects. |
| `.bin` | Pickle serialization format with similar execution risks. |
| `.safetensors` | Stores only model weights and removes pickle serialization attacks. |
| `.h5` | Native Keras format capable of containing executable architecture-level code. |
| `.gguf` | Local large language model format that does not rely on pickle serialization. |

---

### Security Findings

The investigation demonstrated that model format directly influences exposure to serialization attacks.

While SafeTensors removes risks associated with pickle serialization, other attack types remain possible depending on how the model was developed.

---

## Quantization Assessment

The learning material explains that GGUF models are commonly distributed in a quantized format.

Quantization reduces the size of model weights, allowing large language models to operate on systems with fewer computational resources.

If the quantization process is performed by a third party, that transformation itself becomes another trust relationship within the AI supply chain.

---

## Hosted AI Services

Organizations increasingly consume AI through hosted API services instead of downloading model files directly.

Under this deployment model, prompts are sent to an external provider, which performs inference within its own infrastructure before returning a response.

The organization never accesses the underlying model file.

---

## Deployment Model Comparison

| Supply Chain Element | Downloaded Model | Hosted API |
|----------------------|------------------|------------|
| Model Weights | Downloaded and locally inspected | Managed by the provider |
| Training Data | Often documented | Frequently undisclosed |
| Fine-Tuning | Controlled by the organization | Controlled by the provider |
| System Prompts | Not applicable | Managed by the provider |
| Versioning | Specific model versions can be selected | Models may change behind the same endpoint |
| Hosting | Organization infrastructure | Provider infrastructure |

---

### Security Findings

Although hosted AI services eliminate risks associated with loading local model files, organizations continue to rely on external trust relationships.

Instead of trusting downloaded artifacts, organizations trust the provider's infrastructure, training practices, fine-tuning decisions, and deployment pipeline.



# Key Findings

- AI models can be consumed through local downloads or hosted APIs.
- Both deployment approaches rely on external trust relationships.
- Model file formats influence serialization-related risks.
- Hosted AI services shift trust from downloaded artifacts to external providers.

---


# AI Supply Chain Attack Surface Analysis

## Objective

Investigate the primary attack layers within an AI supply chain and understand how attackers compromise trusted AI components before they reach downstream users.

---

### Activities Performed

- Identified the four AI supply chain attack layers
- Investigated model-layer attacks
- Examined dependency-based attacks
- Reviewed dataset poisoning risks
- Explored infrastructure compromise scenarios
- Analyzed multi-layer supply chain attacks

---

## AI Supply Chain Attack Layers

Unlike conventional cyberattacks that target deployed applications directly, AI supply chain attacks focus on compromising trusted components before they are integrated into an AI environment.

The AI supply chain consists of four primary attack layers.

| Attack Layer | Primary Target |
|--------------|----------------|
| Model Layer | AI model files and trained weights |
| Dependency Layer | Software packages and machine learning libraries |
| Data Layer | Training and evaluation datasets |
| Infrastructure Layer | Model repositories, package registries, and build infrastructure |

Each layer represents a unique attack surface capable of compromising AI systems before deployment.



# Model Layer Assessment

## Objective

Investigate attacks targeting AI model files and understand how malicious functionality can be embedded into models before deployment.

---

### Activities Performed

- Examined model-layer attack techniques
- Identified serialization-based attacks
- Investigated architecture-level attacks
- Reviewed weight-level attacks
- Compared attack persistence across model formats

---

## Model Layer Investigation

The model layer represents the most distinctive attack surface within the AI supply chain.

Rather than attacking software dependencies, attackers target the AI model itself by embedding malicious behavior within model files or modifying how models behave after deployment.

The learning material identifies three categories of model-layer attacks.

| Attack Type | Description |
|-------------|-------------|
| Serialization-Level | Malicious code executes when the model file is loaded. |
| Architecture-Level | Malicious logic is embedded within the model architecture. |
| Weights-Level | Trained weights are modified to produce hidden behavior under specific conditions. |

---

## Serialization-Level Attacks

Serialization attacks exploit executable objects embedded within model files.

When the model is loaded into memory, the serialized object executes automatically before inference begins.

According to the learning material, this behavior is associated with pickle-based model formats.

---

## Architecture-Level Attacks

Architecture-level attacks modify the internal structure of the model itself.

Unlike serialization attacks, the malicious behavior becomes part of the model's architecture and executes during prediction.

Changing the model file format alone does not eliminate this attack.

---

## Weights-Level Attacks

Weights-level attacks manipulate the model's learned parameters.

Rather than executing immediately, the model behaves normally until specific inputs trigger hidden behavior introduced during training.

---

### Security Findings

The investigation demonstrated that model attacks operate at multiple levels.

Although converting models to **SafeTensors** removes serialization-based attacks, it does not eliminate architecture-level or weights-level attacks.

Protecting AI models therefore requires evaluating more than the model file format alone.

---

# Key Findings

- Model-layer attacks extend beyond executable serialization.
- SafeTensors eliminates serialization attacks only.
- Architecture-level and weights-level attacks remain possible after format conversion.

---

# Dependency Layer Assessment

## Objective

Investigate how software dependencies introduce additional attack surfaces into AI environments.

---

### Activities Performed

- Examined supporting software dependencies
- Investigated dependency confusion
- Reviewed typosquatting attacks
- Identified malicious package distribution

---

## Dependency Layer Investigation

Machine learning frameworks rely on numerous external packages to function correctly.

Attackers exploit this trust by publishing malicious packages that closely resemble legitimate software.

The learning material highlights several common dependency attacks, including:

- Dependency confusion
- Typosquatting
- Malicious packages disguised as legitimate software

Because these packages originate from trusted repositories, developers may unknowingly install malicious software into their AI environments.

---

### Security Findings

The assessment demonstrated that dependency attacks target software developers rather than AI models directly.

Compromising supporting packages allows attackers to gain access before machine learning applications are executed.

---

# Key Findings

- Supporting packages remain an important AI supply chain attack surface.
- Dependency attacks exploit developer trust in public package repositories.
- Malicious packages may closely resemble legitimate software.

---

# Data Layer Assessment

## Objective

Understand how compromised datasets influence AI model behavior before deployment.

---

### Activities Performed

- Investigated training datasets
- Examined poisoned dataset scenarios
- Reviewed supply chain risks affecting externally sourced data

---

## Data Layer Investigation

Training data forms the foundation of every machine learning model.

The learning material explains that attackers can introduce carefully crafted samples into publicly available datasets before organizations use them for training.

Even though the model continues to perform normally on legitimate inputs, hidden behaviors may be introduced during the training process.

Within this project, the focus remains on understanding how compromised datasets become part of the AI supply chain rather than the mechanics of data poisoning itself.

---

### Security Findings

The assessment highlighted that training datasets represent another trusted external resource.

Organizations that obtain datasets from unverified sources inherit the associated supply chain risks throughout model development.

---

# Key Findings

- AI models inherit behavior from their training data.
- Externally sourced datasets expand the AI supply chain.
- Trust relationships extend beyond software into training data.

---

# Infrastructure Layer Assessment

## Objective

Investigate attacks targeting the infrastructure responsible for storing, managing, and distributing AI resources.

---

### Activities Performed

- Examined repository compromise
- Investigated build pipeline attacks
- Reviewed maintainer credential theft
- Studied malicious software distribution through trusted infrastructure

---

## Infrastructure Investigation

Infrastructure attacks target the systems responsible for distributing trusted AI resources rather than the resources themselves.

Examples presented in the learning material include:

- Compromised model repositories
- Malicious build pipeline modifications
- Stolen maintainer credentials
- Malicious updates distributed through trusted accounts

Because these systems serve trusted software to downstream users, compromising infrastructure enables attackers to distribute malicious content at scale.

---

### Security Findings

Infrastructure attacks exploit trusted distribution mechanisms instead of AI models.

Compromising trusted repositories allows malicious software to be delivered through legitimate update processes.

---

# Key Findings

- Infrastructure forms a critical component of the AI supply chain.
- Trusted repositories become high-value targets.
- Compromised infrastructure can affect numerous downstream users simultaneously.

---

# Multi-Layer Attack Analysis

## Objective

Understand how attackers combine multiple supply chain attack layers during a single compromise.

---

### Activities Performed

- Reviewed multi-stage attack scenarios
- Identified attacks spanning multiple supply chain layers
- Investigated how individual compromises combine into larger attacks

---

## Multi-Layer Investigation

The four attack layers do not operate independently.

The learning material describes an example where an attacker:

1. Compromises a Hugging Face account.
2. Uploads a backdoored model containing malicious pickle code.
3. Includes a requirements file referencing malicious dependencies.
4. Distributes a poisoned dataset alongside the model.

Rather than targeting a single component, the attacker compromises multiple trusted resources before the AI system reaches production.

---

### Security Findings

The assessment demonstrated that AI supply chain attacks become significantly more effective when multiple attack layers are combined.

A single downloaded project may simultaneously expose an organization to risks affecting models, dependencies, datasets, and supporting infrastructure.

---

# Key Findings

- AI supply chain attacks may span multiple attack layers.
- A single compromise can impact numerous trusted components.
- Attackers often combine techniques to maximize downstream impact.
