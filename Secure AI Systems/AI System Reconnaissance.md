# AI System Reconnaissance & Attack Surface Assessment

> Performing reconnaissance against enterprise AI infrastructure to identify exposed machine learning services, enumerate AI frameworks, assess attack surfaces, and document security risks through evidence-driven analysis.

---

# Project Overview

Artificial Intelligence has become a critical component of modern enterprise environments, powering internal copilots, machine learning pipelines, inference servers, vector databases, and large language model (LLM) applications. While these technologies accelerate business operations, they also introduce new attack surfaces that traditional security assessments often overlook.

This project demonstrates a structured security assessment of an enterprise AI environment belonging to **Cyphira**, focusing on identifying exposed AI services, fingerprinting machine learning frameworks, enumerating inference endpoints, and evaluating the overall AI attack surface.

Rather than relying solely on automated scanners, the assessment combines network reconnaissance, service fingerprinting, HTTP enumeration, API discovery, and manual validation to accurately identify AI components deployed within the environment.

Every discovery documented in this repository is supported by the commands executed, terminal outputs, and analyst validation, reflecting the methodology used during professional infrastructure assessments.

---

# Assessment Objectives

The assessment aimed to answer the following questions:

- Which AI services are exposed on the network?
- Which machine learning frameworks are deployed?
- Are inference APIs publicly accessible?
- Are metadata endpoints exposed?
- Are vector databases properly secured?
- Are AI development environments discoverable?
- Which components increase the organization's attack surface?
- Which security controls should be implemented?

---

# Skills Demonstrated

## AI Security

- AI Infrastructure Discovery
- AI Attack Surface Assessment
- AI Framework Identification
- AI Service Enumeration
- AI API Fingerprinting
- AI Security Validation

## Network Reconnaissance

- Network Enumeration
- Port Scanning
- Service Fingerprinting
- HTTP Enumeration
- API Discovery
- gRPC Enumeration

## Security Assessment

- Attack Surface Analysis
- Asset Discovery
- Risk Identification
- Exposure Validation
- Security Recommendations

---

# Technologies Used

## Operating System

- Kali Linux

## Reconnaissance

- Nmap
- curl
- grpcurl
- Bash

## AI Platforms

- MLflow
- Triton Inference Server
- Qdrant
- Jupyter Notebook

---

# Assessment Workflow

```text
Target Identification
        │
        ▼
Port Discovery
        │
        ▼
Service Fingerprinting
        │
        ▼
Framework Identification
        │
        ▼
API Enumeration
        │
        ▼
Metadata Discovery
        │
        ▼
Human Validation
        │
        ▼
Risk Assessment
        │
        ▼
Security Recommendations
```

---

# Assessment Methodology

Unlike a theoretical overview, this repository documents every stage of the assessment using the exact commands executed, supporting evidence, analyst validation, and security analysis.

Each phase includes:

- Objective
- Background
- Investigation
- Commands Executed
- Evidence
- Proof of Work
- Human Validation
- Technical Analysis
- MITRE ATLAS / MITRE ATT&CK Mapping
- Security Recommendations
- Lessons Learned

---

# Practical Assessment

## Phase 1 — AI Infrastructure Discovery

### Objective

Identify AI-related services running within the Cyphira environment.

---

### Background

Before assessing the security of AI applications, it is necessary to determine which AI services are present. Common enterprise AI deployments expose well-known ports associated with machine learning platforms, inference servers, vector databases, and development environments.

The first step of the assessment focused on discovering these services.

---

### Investigation

A targeted service discovery scan was performed against ports commonly associated with AI infrastructure.

### Commands Executed

```bash
nmap -Pn \
-p 5000,6333,6334,8000,8001,8002,8888 \
-sV \
cyphira.internal
```

---

### Evidence

```text
PORT     STATE SERVICE

5000/tcp open  http

8000/tcp open  http

8001/tcp open  grpc

8002/tcp open  http

6333/tcp open  http

6334/tcp open  grpc

8888/tcp open  http
```

---

### Proof of Work

The scan confirmed that multiple AI-specific services were reachable, indicating the presence of a machine learning environment rather than a traditional application stack.

Evidence collected during this phase became the foundation for subsequent framework fingerprinting and API enumeration activities.

---

### Human Validation

Rather than assuming the detected services were AI platforms based solely on port numbers, each service was manually validated using HTTP requests and protocol-specific enumeration techniques.

This step reduced the likelihood of false positives and ensured accurate identification before documenting the environment.

---

### Technical Analysis

The identified ports strongly suggested the presence of several AI technologies commonly deployed in enterprise environments:

- Port **5000** – Frequently used by MLflow Tracking Server.
- Ports **8000–8002** – Commonly associated with NVIDIA Triton Inference Server (HTTP, gRPC, and metrics).
- Ports **6333–6334** – Default ports for the Qdrant vector database.
- Port **8888** – Commonly used by Jupyter Notebook or JupyterLab.

The discovery of these services indicated that the environment contained components used for model development, model serving, vector search, and data science collaboration, significantly expanding the organization's attack surface beyond conventional web applications.

---

### MITRE ATT&CK / MITRE ATLAS

| Framework | Technique | Description |
|-----------|-----------|-------------|
| MITRE ATT&CK | T1595 | Active Scanning |
| MITRE ATT&CK | T1046 | Network Service Discovery |
| MITRE ATLAS | Reconnaissance | AI Infrastructure Discovery |

---

### Security Recommendations

- Restrict administrative interfaces to trusted networks.
- Disable unnecessary public exposure of AI services.
- Place inference servers behind authenticated reverse proxies.
- Implement network segmentation for AI workloads.
- Continuously inventory AI assets and exposed services.

---



# 02 - Reconnaissance

# Phase 2 — Service Fingerprinting & AI Framework Identification

---

## Objective

Determine the identity of each discovered service by performing application-layer fingerprinting. Rather than relying solely on open ports, this phase validates the underlying AI technologies through HTTP responses, API endpoints, and protocol-specific enumeration.

Successful framework identification enables more accurate risk assessment and determines which platform-specific security checks should be performed during later stages of the assessment.

---

## Background

Port scanning reveals that a service is listening, but it does not confirm which application is actually running.

For example:

- Port **5000** is commonly associated with MLflow, but many applications can use the same port.
- Port **8888** often hosts Jupyter Notebook, although numerous web applications also listen there.
- Ports **8000–8002** are typically associated with Triton Inference Server, but confirmation requires inspection of the application's responses.

To eliminate assumptions, each service was manually fingerprinted.

---

# Investigation

The assessment began with the HTTP services discovered during reconnaissance.

---

## Step 1 — Enumerate HTTP Services

### Command Executed

```bash
curl -I http://cyphira.internal:5000
```

---

### Evidence

```http
HTTP/1.1 200 OK

Server: Werkzeug/3.0.1 Python/3.11

Content-Type: text/html
```

---

### Proof of Work

The response confirms:

- HTTP service available
- Python-based application
- Werkzeug development server

Werkzeug is commonly used by Flask applications, including MLflow.

---

### Human Validation

Although Werkzeug strongly suggests an MLflow deployment, additional validation is required because numerous Flask applications share the same HTTP server.

The assessment therefore continued by querying known MLflow API endpoints.

---

## Step 2 — Query MLflow API

### Command Executed

```bash
curl http://cyphira.internal:5000/api/2.0/mlflow/experiments/list
```

---

### Evidence

```json
{
  "experiments": [
      {
        "experiment_id":"0",
        "name":"Default"
      }
  ]
}
```

---

### Proof of Work

The API response confirmed:

- MLflow REST API available
- Experiment tracking enabled
- Default experiment accessible

The endpoint positively identified the service as an MLflow Tracking Server.

---

### Human Validation

The returned JSON structure matches the official MLflow REST API specification.

This confirms that the service is not merely Flask-based, but an active MLflow deployment exposing experiment management functionality.

---

### Technical Analysis

MLflow Tracking Servers often store:

- Experiment metadata
- Model versions
- Run history
- Parameters
- Metrics
- Artifact locations

If improperly secured, these resources may expose sensitive information about an organization's machine learning pipeline.

---

### MITRE ATT&CK / MITRE ATLAS

| Framework | Technique | Description |
|-----------|-----------|-------------|
| ATT&CK | T1595 | Active Scanning |
| ATT&CK | T1046 | Network Service Discovery |
| MITRE ATLAS | AI Infrastructure Discovery | AI Platform Enumeration |

---

# Phase 3 — Triton Inference Server Enumeration

---

## Objective

Identify inference services exposed by the NVIDIA Triton Inference Server.

---

## Background

Inference servers host machine learning models and process prediction requests.

If exposed without authentication, they may reveal:

- Available models
- Framework versions
- Input schemas
- Output schemas
- Health information
- Performance metrics

---

## Investigation

Inspect the Triton HTTP endpoint.

### Command Executed

```bash
curl http://cyphira.internal:8000/v2
```

---

### Evidence

```json
{
  "name":"triton",
  "version":"2.46.0",
  "extensions":[
      "classification",
      "sequence",
      "model_repository"
  ]
}
```

---

### Proof of Work

The endpoint exposed:

- Triton version
- Supported extensions
- Model repository support

This positively identified an NVIDIA Triton Inference Server.

---

### Human Validation

The returned API format matches NVIDIA's official inference protocol.

The server was therefore classified as a production inference endpoint.

---

## Enumerate Available Models

### Command Executed

```bash
curl http://cyphira.internal:8000/v2/models
```

---

### Evidence

```json
[
  {
      "name":"fraud_detection"
  },
  {
      "name":"document_classifier"
  },
  {
      "name":"internal_llm"
  }
]
```

---

### Proof of Work

The inference server disclosed three deployed machine learning models without requiring authentication.

This demonstrates how model enumeration can expose valuable intelligence about an organization's AI capabilities.

---

### Technical Analysis

Model names frequently reveal:

- Internal business functions
- Detection capabilities
- Proprietary research
- AI deployment strategy

Such information may assist an attacker during reconnaissance or enable targeted attacks against specific models.

---

### Security Recommendations

- Require authentication before exposing inference APIs.
- Restrict model repository access.
- Disable unnecessary metadata endpoints.
- Monitor requests to inference services.
- Place inference servers behind API gateways.

---

### Lessons Learned

AI inference servers often expose significantly more operational metadata than traditional web applications. Even without model access, publicly available endpoint information can reveal an organization's AI architecture, deployed capabilities, and potential attack paths.

---

# Phase 4 — Qdrant Vector Database Enumeration

---

## Objective

Determine whether the discovered Qdrant service exposes vector collections, metadata, or administrative functionality that could increase the organization's AI attack surface.

---

## Background

Vector databases have become foundational components of Retrieval-Augmented Generation (RAG) applications, semantic search engines, recommendation systems, and enterprise LLM deployments.

Unlike traditional databases, vector databases store high-dimensional embeddings generated from sensitive enterprise information such as:

- Internal documentation
- Source code
- Customer records
- Knowledge bases
- Emails
- Proprietary research
- AI training datasets

If exposed without proper authentication, attackers may enumerate collections, extract metadata, infer business operations, or abuse the database during prompt injection and data exfiltration attacks.

Because Qdrant was identified during the reconnaissance phase, additional validation was required.

---

# Investigation

## Step 1 — Verify the HTTP Endpoint

### Command Executed

```bash
curl http://cyphira.internal:6333
```

---

### Evidence

```json
{
    "title":"qdrant",
    "version":"1.9.0"
}
```

---

### Proof of Work

The HTTP response confirmed:

- Qdrant is running
- Version information disclosed
- HTTP API accessible

This validates that the service identified during reconnaissance is a Qdrant Vector Database.

---

### Human Validation

The response format matches the official Qdrant API documentation.

No authentication challenge was presented, indicating that public metadata is accessible.

---

## Step 2 — Enumerate Available Collections

### Command Executed

```bash
curl http://cyphira.internal:6333/collections
```

---

### Evidence

```json
{
  "result":{
      "collections":[
          {
             "name":"documents"
          },
          {
             "name":"embeddings"
          },
          {
             "name":"research_notes"
          }
      ]
  }
}
```

---

### Proof of Work

Three vector collections were successfully enumerated.

The collection names reveal:

- Enterprise documentation
- Machine learning embeddings
- Internal research assets

Collection naming conventions frequently disclose valuable intelligence regarding organizational operations.

---

### Technical Analysis

Although the vectors themselves were not extracted, metadata alone provides meaningful reconnaissance value.

The existence of a collection named **research_notes** suggests that sensitive internal information may be indexed for AI retrieval.

Attackers could leverage this intelligence when planning:

- Prompt Injection attacks
- Retrieval poisoning
- Data exfiltration
- Model inversion attempts

---

## Step 3 — Inspect Collection Metadata

### Command Executed

```bash
curl http://cyphira.internal:6333/collections/documents
```

---

### Evidence

```json
{
  "status":"green",
  "vectors_count":184512,
  "segments_count":18,
  "optimizer_status":"ok"
}
```

---

### Proof of Work

Metadata disclosed:

- Number of indexed vectors
- Segment count
- Cluster health
- Optimizer status

Although no document contents were exposed, infrastructure metadata reveals the scale of the AI deployment.

---

### Human Validation

The endpoint returned operational statistics without requiring authentication.

This behavior was manually confirmed using repeated requests.

---

## Step 4 — Validate gRPC Service

Many enterprise deployments expose Qdrant's gRPC interface in addition to the REST API.

The assessment validated whether the service was accessible.

### Command Executed

```bash
grpcurl \
plaintext \
cyphira.internal:6334 list
```

---

### Evidence

```text
grpc.health.v1.Health

qdrant.Collections

qdrant.Points

qdrant.Snapshots
```

---

### Proof of Work

The exposed services indicate that administrative functionality is reachable through gRPC.

Available services include:

- Collection management
- Point operations
- Snapshot management
- Health monitoring

---

### Human Validation

The returned services align with the standard Qdrant gRPC interface.

Although enumeration was successful, no administrative actions were performed because the assessment was limited to reconnaissance.

---

# Technical Analysis

The assessment identified multiple exposed interfaces that significantly expand the organization's AI attack surface.

While no unauthorized access was attempted, publicly accessible metadata may assist attackers by revealing:

- AI architecture
- Database scale
- Collection naming conventions
- Operational health
- Administrative capabilities

Such information can be combined with subsequent attacks targeting Retrieval-Augmented Generation (RAG) pipelines or vector database misconfigurations.

---

# MITRE ATT&CK / MITRE ATLAS

| Framework | Technique | Description |
|-----------|-----------|-------------|
| MITRE ATT&CK | T1046 | Network Service Discovery |
| MITRE ATT&CK | T1595 | Active Scanning |
| MITRE ATLAS | AI Infrastructure Discovery | Enumeration of AI Components |
| MITRE ATLAS | Reconnaissance | AI Asset Identification |

---

# Detection Opportunities

### Sigma Concept

Detect repeated requests to vector database management endpoints.

```yaml
title: Suspicious Enumeration of Qdrant Collections

logsource:
  product: webserver

detection:
  selection:
    cs-uri-stem|contains:
      - "/collections"
  condition: selection
```

---

### Additional Monitoring Recommendations

- Alert on repeated requests to `/collections`.
- Monitor unauthenticated access to vector database APIs.
- Log gRPC administrative requests.
- Detect abnormal collection enumeration.
- Audit snapshot creation and download events.

---

# Security Recommendations

- Require authentication for all REST and gRPC endpoints.
- Disable public access to administrative APIs.
- Restrict vector database access using network segmentation.
- Place Qdrant behind an authenticated API gateway.
- Regularly audit exposed collections and metadata endpoints.
- Encrypt sensitive embeddings at rest.
- Enable detailed audit logging for collection access.

---

# Lessons Learned

Vector databases should be treated as high-value enterprise assets rather than traditional databases. Even when document contents remain protected, exposed metadata can reveal valuable information about an organization's AI capabilities, internal knowledge repositories, and operational architecture.

This phase demonstrates that AI security assessments must extend beyond conventional network reconnaissance to include AI-specific infrastructure components that are increasingly common in modern enterprise environments.


---


# Phase 5 — Jupyter Notebook Enumeration

---

## Objective

Identify whether an exposed Jupyter Notebook environment is accessible, determine its authentication status, and assess the potential security impact of exposing AI development environments.

---

## Background

Jupyter Notebook and JupyterLab are widely used by Data Scientists, Machine Learning Engineers, and AI Researchers for:

- Model development
- Dataset exploration
- Feature engineering
- Experimentation
- Model training
- API testing
- Infrastructure administration

Unlike production inference servers, Jupyter environments frequently contain:

- API keys
- Cloud credentials
- Model checkpoints
- Source code
- Internal datasets
- Database credentials
- Training notebooks

For this reason, exposed Jupyter environments represent one of the most critical attack surfaces within AI deployments.

---

# Investigation

Following the discovery of TCP port **8888**, the service was manually inspected to determine whether it was hosting a Jupyter environment.

---

## Step 1 — Inspect HTTP Headers

### Command Executed

```bash
curl -I http://cyphira.internal:8888
```

---

### Evidence

```http
HTTP/1.1 200 OK

Server: TornadoServer/6.4

Content-Type: text/html
```

---

### Proof of Work

The HTTP response identified the service as a Tornado-based web application.

Jupyter Notebook and JupyterLab are built on the Tornado framework, making this a strong indicator that a notebook environment is present.

---

### Human Validation

Although Tornado strongly suggests Jupyter, additional validation was required before documenting the service.

---

## Step 2 — Inspect the Landing Page

### Command Executed

```bash
curl http://cyphira.internal:8888
```

---

### Evidence

```html
<title>JupyterLab</title>

Please supply the authentication token.
```

---

### Proof of Work

The landing page confirmed:

- JupyterLab deployment
- Authentication enabled
- Token-based access control

This positively identified the service as an authenticated JupyterLab instance.

---

### Human Validation

The page structure and authentication prompt matched a default JupyterLab deployment.

No attempt was made to bypass authentication because the assessment scope was limited to reconnaissance.

---

## Step 3 — Enumerate Public API Endpoints

Several public endpoints were queried to determine whether metadata was exposed prior to authentication.

### Command Executed

```bash
curl http://cyphira.internal:8888/api
```

---

### Evidence

```json
{
    "message":"Forbidden"
}
```

---

### Proof of Work

The server rejected unauthenticated API requests.

This indicates that API endpoints require authentication before returning notebook metadata.

---

### Technical Analysis

Although authentication was correctly enforced, the server still disclosed:

- Framework identity
- Authentication mechanism
- Technology stack

Such information assists attackers during reconnaissance by narrowing exploit selection.

---

## Step 4 — Identify Running Kernels

A common post-authentication endpoint was queried to determine whether kernel information was exposed.

### Command Executed

```bash
curl http://cyphira.internal:8888/api/kernels
```

---

### Evidence

```json
{
    "message":"Forbidden"
}
```

---

### Proof of Work

Kernel information was not exposed without authentication.

This demonstrates that notebook execution sessions were protected against anonymous enumeration.

---

### Human Validation

Repeated requests produced identical responses, confirming that access controls were consistently enforced.

---

# Technical Analysis

Unlike many publicly exposed Jupyter deployments that allow anonymous notebook access, the Cyphira environment enforced token-based authentication before exposing notebook metadata.

While this represents an important security control, reconnaissance still revealed valuable information, including:

- JupyterLab deployment
- Tornado framework
- Authentication method
- Accessible endpoints
- Technology stack

Such intelligence could be leveraged during future attack planning or vulnerability research.

---

# MITRE ATT&CK / MITRE ATLAS

| Framework | Technique | Description |
|-----------|-----------|-------------|
| MITRE ATT&CK | T1595 | Active Scanning |
| MITRE ATT&CK | T1046 | Network Service Discovery |
| MITRE ATLAS | AI Infrastructure Discovery | AI Development Environment Enumeration |

---

# Detection Opportunities

### Sigma Concept

Detect repeated probing of Jupyter Notebook API endpoints.

```yaml
title: Suspicious Jupyter API Enumeration

logsource:
  product: webserver

detection:
  selection:
    cs-uri-stem|contains:
      - "/api/kernels"
      - "/api/sessions"
      - "/api/contents"

condition: selection
```

---

## Additional Monitoring Recommendations

- Alert on repeated unauthenticated API requests.
- Monitor failed token validation attempts.
- Detect notebook enumeration activity.
- Audit notebook creation and deletion events.
- Log all remote notebook access.

---

# Security Recommendations

- Restrict Jupyter access to trusted administrative networks.
- Require strong authentication (SSO or MFA where possible).
- Rotate notebook authentication tokens regularly.
- Disable anonymous access to notebook APIs.
- Store secrets outside notebook files.
- Continuously monitor notebook activity and API usage.

---

# Lessons Learned

AI development environments frequently contain some of an organization's most valuable intellectual property, including datasets, model code, credentials, and research artifacts. Even when authentication is properly configured, publicly accessible notebook interfaces can reveal valuable reconnaissance information. Security teams should treat Jupyter environments as high-value assets and continuously monitor them for unauthorized access attempts.

---

# Phase 6 — MLflow Experiment Enumeration

---

## Objective

Determine whether the MLflow Tracking Server exposes experiment metadata, model information, or artifact references that could reveal details about the organization's machine learning pipeline.

---

## Background

MLflow is commonly used to manage the machine learning lifecycle, including experiment tracking, parameter logging, model versioning, and artifact storage.

A misconfigured MLflow deployment may expose:

- Experiment names
- Model versions
- Hyperparameters
- Training metrics
- Artifact locations
- Storage backends
- Internal project names

Although this information may not constitute direct data exposure, it can provide attackers with detailed insight into an organization's AI development processes.

---

# Investigation

Following the positive identification of the MLflow Tracking Server during earlier phases, additional enumeration was performed against its REST API to assess the amount of metadata exposed.

## Step 1 — List Available Experiments

### Command Executed

```bash
curl http://cyphira.internal:5000/api/2.0/mlflow/experiments/search
```

---

### Evidence

```json
{
  "experiments": [
    {
      "experiment_id": "1",
      "name": "Fraud Detection Model"
    },
    {
      "experiment_id": "2",
      "name": "Customer Churn Prediction"
    },
    {
      "experiment_id": "3",
      "name": "Internal LLM Evaluation"
    }
  ]
}
```

---

### Proof of Work

The API returned multiple experiment names without requiring elevated interaction.

These names reveal active AI initiatives and provide insight into the organization's machine learning portfolio.

---

### Human Validation

The response structure matched the official MLflow API schema, confirming that the metadata originated from the MLflow Tracking Server rather than a custom application.

---

### Technical Analysis

Experiment names alone can disclose valuable business intelligence. In this case, they suggest ongoing work in fraud detection, customer analytics, and internal large language model evaluation—information that could aid a targeted attacker in prioritizing assets or crafting social engineering campaigns.

The assessment continued by reviewing accessible run metadata and artifact references in subsequent steps.


---

# Phase 7 — AI Model & Metadata Enumeration

---

## Objective

Assess whether the identified AI infrastructure exposes model metadata, experiment information, artifact references, or configuration details that could provide an attacker with intelligence about the organization's machine learning ecosystem.

---

## Background

Modern AI platforms rarely expose trained models directly. Instead, attackers often target metadata that reveals how those models are built, trained, deployed, and managed.

Metadata reconnaissance can reveal:

- Model names
- Framework versions
- Training experiments
- Artifact locations
- Storage backends
- Deployment pipelines
- Internal project names
- Business functions supported by AI

While this information may appear harmless, it enables attackers to understand the organization's AI capabilities without ever compromising the models themselves.

---

# Investigation

Following identification of the MLflow Tracking Server, additional API endpoints were examined to determine the amount of information exposed through the management interface.

---

## Step 1 — Enumerate Registered Models

### Command Executed

```bash
curl http://cyphira.internal:5000/api/2.0/mlflow/registered-models/search
```

---

### Evidence

```json
{
  "registered_models": [
    {
      "name": "fraud_detection"
    },
    {
      "name": "customer_churn"
    },
    {
      "name": "internal_llm"
    }
  ]
}
```

---

### Proof of Work

The MLflow Model Registry disclosed three registered models.

Although model binaries were not downloaded, the registry exposed information about deployed AI capabilities within the organization.

---

### Human Validation

The API response structure matched the official MLflow Model Registry specification.

This confirmed that the exposed metadata originated from MLflow rather than a custom application.

---

### Technical Analysis

Model names often reveal:

- Business priorities
- Security capabilities
- Proprietary AI research
- Internal project code names

Knowledge that an organization operates an **Internal LLM** may encourage attackers to target prompt injection or model extraction attacks.

---

## Step 2 — Enumerate Experiment Runs

### Command Executed

```bash
curl http://cyphira.internal:5000/api/2.0/mlflow/runs/search
```

---

### Evidence

```json
{
    "runs":[
        {
            "run_id":"cb13...",
            "status":"FINISHED",
            "artifact_uri":"s3://ml-artifacts/run-001"
        }
    ]
}
```

---

### Proof of Work

The exposed run metadata revealed:

- Completed experiments
- Artifact storage locations
- Experiment lifecycle information

Although artifacts themselves were not accessed, the metadata provides intelligence regarding storage architecture.

---

### Human Validation

Returned values matched the expected MLflow Run object schema.

No attempt was made to access referenced artifacts because the assessment scope was limited to reconnaissance.

---

## Step 3 — Inspect Artifact References

### Command Executed

```bash
curl http://cyphira.internal:5000/api/2.0/mlflow/artifacts/list?run_id=cb13...
```

---

### Evidence

```json
{
  "files":[
      "model.pkl",
      "requirements.txt",
      "metrics.json",
      "training.log"
  ]
}
```

---

### Proof of Work

Artifact metadata disclosed the structure of stored experiment outputs.

This indicates that experiment artifacts are centrally managed and may include trained models, dependency manifests, and execution logs.

---

### Technical Analysis

Artifact listings provide attackers with insight into:

- Machine learning frameworks
- Dependency versions
- Serialization formats
- Potential supply chain weaknesses

For example, a serialized `.pkl` model suggests Python Pickle, which has security implications if artifacts are loaded from untrusted sources.

---

# MITRE ATT&CK / MITRE ATLAS

| Framework | Technique | Description |
|-----------|-----------|-------------|
| MITRE ATT&CK | T1592 | Gather Victim Host Information |
| MITRE ATT&CK | T1595 | Active Scanning |
| MITRE ATLAS | AI Model Discovery | AI Asset Enumeration |

---

# Detection Opportunities

### Sigma Concept

```yaml
title: Suspicious MLflow Model Enumeration

logsource:
  product: webserver

detection:
  selection:
    cs-uri-stem|contains:
      - "/registered-models"
      - "/runs/search"
      - "/artifacts"

condition: selection
```

---

## Additional Monitoring Recommendations

- Detect repeated access to MLflow API endpoints.
- Audit model registry enumeration.
- Monitor artifact listing requests.
- Alert on unusual metadata scraping patterns.
- Enable authentication logs for MLflow API usage.

---

# Security Recommendations

- Require authentication for all MLflow management APIs.
- Limit model registry visibility based on user roles.
- Prevent anonymous artifact enumeration.
- Store experiment artifacts in secured object storage.
- Enable audit logging for model registry operations.

---

# Lessons Learned

Model metadata is often as valuable as the models themselves. Even when trained models remain protected, exposed experiment information can reveal an organization's AI maturity, deployment strategy, storage architecture, and operational priorities. Restricting access to AI management metadata is therefore a critical component of AI infrastructure security.

---

# Phase 8 — AI Attack Surface Assessment

---

## Objective

Consolidate reconnaissance findings into a structured assessment of the organization's AI attack surface and identify areas requiring immediate security improvements.

---

## Background

An AI environment consists of far more than a single model endpoint. The attack surface spans every component involved in the machine learning lifecycle, including development environments, experiment tracking platforms, inference servers, vector databases, storage systems, and supporting APIs.

Understanding these interconnected assets enables defenders to prioritize remediation efforts and strengthen the overall security posture of AI systems.

---

# Assessment Summary

The investigation identified multiple externally reachable AI services:

| Service | Status | Risk |
|---------|--------|------|
| MLflow Tracking Server | Accessible | Medium |
| Triton Inference Server | Accessible | High |
| Qdrant Vector Database | Accessible | High |
| JupyterLab | Accessible | Medium |

---

# Evidence Summary

The following activities were successfully completed during the assessment:

- Network reconnaissance
- Service fingerprinting
- Framework identification
- REST API enumeration
- gRPC service discovery
- MLflow metadata inspection
- Triton model enumeration
- Vector database assessment
- Jupyter authentication review

Each finding was manually validated before inclusion in this report.

---

# Attack Surface Analysis

## AI Development

**Technology Identified**

- JupyterLab
- MLflow

**Primary Risks**

- Notebook exposure
- Credential leakage
- Artifact disclosure

---

## AI Inference

**Technology Identified**

- Triton Inference Server

**Primary Risks**

- Model enumeration
- Inference abuse
- Metadata disclosure

---

## AI Knowledge Retrieval

**Technology Identified**

- Qdrant Vector Database

**Primary Risks**

- Collection enumeration
- Metadata exposure
- Retrieval abuse

---

## Machine Learning Lifecycle

**Technology Identified**

- MLflow Tracking

**Primary Risks**

- Experiment disclosure
- Artifact exposure
- Pipeline intelligence gathering

---

# Overall Risk Rating

| Category | Risk |
|----------|------|
| AI Asset Discovery | High |
| Metadata Exposure | Medium |
| Model Intelligence Leakage | High |
| AI Infrastructure Enumeration | High |
| Administrative Interface Exposure | Medium |

---

# Executive Recommendations

1. Restrict all AI administrative interfaces to trusted management networks.
2. Enforce authentication and authorization across AI APIs.
3. Segment AI infrastructure from general corporate networks.
4. Implement centralized logging and monitoring for AI services.
5. Regularly inventory AI assets and exposed endpoints.
6. Conduct periodic AI-specific attack surface assessments.
7. Align AI security controls with the NIST AI Risk Management Framework and MITRE ATLAS.

---

# Lessons Learned

This assessment demonstrates that enterprise AI environments introduce a unique attack surface that extends beyond traditional web applications. Services such as MLflow, Triton, Qdrant, and Jupyter expose valuable operational metadata that can assist attackers during reconnaissance if left insufficiently protected.

By combining network reconnaissance, protocol-specific enumeration, manual validation, and AI-focused risk analysis, defenders can gain a comprehensive understanding of their AI infrastructure and implement targeted controls to reduce exposure before adversaries exploit these systems.

---


# 04 - Defensive Framework Mapping

# Phase 9 — MITRE ATLAS Mapping

---

## Objective

Correlate the reconnaissance activities performed during this assessment with the MITRE Adversarial Threat Landscape for Artificial Intelligence Systems (ATLAS) framework.

Mapping observed techniques to MITRE ATLAS provides defenders with a standardized method of understanding AI-specific threats while improving detection engineering and defensive planning.

---

## Background

Traditional penetration testing frameworks focus on operating systems, networks, and enterprise applications.

AI systems introduce additional attack surfaces, including:

- Model lifecycle platforms
- Inference APIs
- Prompt interfaces
- Vector databases
- AI development environments
- Training pipelines

MITRE ATLAS extends ATT&CK by documenting adversarial techniques targeting AI systems throughout their lifecycle.

---

# Assessment Mapping

| Assessment Activity | MITRE ATLAS Technique | Description |
|--------------------|----------------------|-------------|
| AI Asset Discovery | AI Infrastructure Discovery | Identification of deployed AI components |
| Port Scanning | Active Scanning | Discovery of AI services |
| MLflow Enumeration | ML Pipeline Enumeration | Discovery of model lifecycle infrastructure |
| Triton Enumeration | AI Model Discovery | Identification of deployed inference services |
| Qdrant Enumeration | AI Knowledge Base Discovery | Discovery of vector storage infrastructure |
| Jupyter Enumeration | Development Environment Discovery | Identification of AI development systems |
| Metadata Enumeration | AI Information Gathering | Collection of operational intelligence |

---

## Assessment Findings

The assessment demonstrates that AI environments expose infrastructure beyond traditional enterprise assets.

Identified AI components included:

- MLflow Tracking Server
- Triton Inference Server
- Qdrant Vector Database
- JupyterLab

Each component introduces unique attack paths that require AI-specific security controls.

---

## Human Validation

Every AI platform identified during reconnaissance was manually validated using protocol-specific techniques rather than relying solely on automated banner identification.

This reduced false positives and ensured that MITRE ATLAS mappings accurately reflected the deployed technologies.

---

# Defensive Value

Using MITRE ATLAS enables organizations to:

- Standardize AI threat modeling.
- Develop AI-focused detection rules.
- Improve AI asset visibility.
- Prioritize AI security controls.
- Strengthen AI governance.

---

# Lessons Learned

MITRE ATLAS provides a structured framework for understanding threats that specifically target AI systems. Integrating ATLAS into AI security assessments helps defenders move beyond traditional infrastructure testing and evaluate risks unique to machine learning environments.

---

# Phase 10 — OWASP LLM Top 10 Mapping

---

## Objective

Evaluate assessment findings against the OWASP Top 10 for Large Language Model Applications to identify AI-specific risks.

---

## Background

The OWASP LLM Top 10 extends traditional application security guidance by addressing vulnerabilities unique to AI systems, including prompt injection, insecure output handling, excessive agency, and model supply chain risks.

Although this assessment focused on infrastructure reconnaissance, several observations align with risks described by OWASP.

---

# Findings

| Repository Finding | OWASP LLM Top 10 Category | Assessment |
|-------------------|---------------------------|------------|
| Public AI APIs | LLM01 - Prompt Injection Exposure | Potential future risk |
| MLflow Metadata Exposure | LLM06 - Sensitive Information Disclosure | Medium |
| Jupyter Development Environment | LLM08 - Excessive Agency | Medium |
| Model Registry Enumeration | LLM04 - Model Theft | Medium |
| Triton Inference Metadata | LLM10 - Model Enumeration | Medium |

---

## Technical Analysis

No prompt injection attacks were performed because the assessment remained within the approved reconnaissance scope.

However, exposed inference endpoints and model metadata demonstrate conditions that could facilitate future AI-focused attacks if additional vulnerabilities were present.

---

## Security Recommendations

- Secure inference APIs behind authentication.
- Restrict metadata visibility.
- Harden notebook environments.
- Limit model registry access.
- Monitor inference requests for abuse.

---

# Lessons Learned

OWASP guidance highlights that securing AI infrastructure requires more than protecting models. Administrative interfaces, metadata, development environments, and supporting services all contribute to the overall security posture.

---

# Phase 11 — NIST AI Risk Management Framework (AI RMF) Alignment

---

## Objective

Assess how the identified findings relate to the NIST Artificial Intelligence Risk Management Framework (AI RMF).

---

## Background

The NIST AI RMF provides organizations with guidance for governing, mapping, measuring, and managing risks introduced by AI systems.

Unlike vulnerability-focused frameworks, AI RMF emphasizes continuous governance and responsible AI deployment.

---

# Assessment Alignment

| NIST AI RMF Function | Assessment Contribution |
|----------------------|-------------------------|
| Govern | AI asset inventory and documentation |
| Map | Identification of AI infrastructure and risks |
| Measure | Evaluation of exposed services and metadata |
| Manage | Security recommendations and remediation planning |

---

## Human Validation

Assessment findings were reviewed manually before being incorporated into governance recommendations.

This ensured that remediation advice reflected validated observations rather than automated assumptions.

---

# Executive Recommendations

- Establish an AI asset inventory.
- Continuously monitor AI infrastructure.
- Restrict administrative interfaces.
- Segment AI workloads.
- Enable centralized logging.
- Regularly assess AI attack surfaces.
- Integrate AI-specific threat modeling into enterprise risk management.

---

# Lessons Learned

Effective AI security requires continuous governance rather than one-time assessments. Organizations should integrate AI infrastructure into existing security programs while adopting controls specifically designed for machine learning systems.

---

# Final Executive Summary

## Engagement Overview

A security assessment was conducted against Cyphira's AI infrastructure to identify exposed AI services, evaluate the organization's machine learning attack surface, and document security risks associated with AI development and deployment environments.

The assessment focused exclusively on reconnaissance and validation activities conducted within an authorized environment.

---

## Assessment Scope

The engagement included:

- Network reconnaissance
- AI service discovery
- Framework identification
- MLflow assessment
- Triton assessment
- Qdrant assessment
- Jupyter assessment
- Metadata enumeration
- AI attack surface analysis

---

## Evidence Summary

The following activities were successfully demonstrated throughout the assessment:

### Network Discovery

```bash
nmap -Pn -sV \
-p5000,6333,6334,8000,8001,8002,8888 \
cyphira.internal
```

---

### Framework Identification

```bash
curl http://cyphira.internal:5000
```

```bash
curl http://cyphira.internal:8000/v2
```

```bash
curl http://cyphira.internal:6333
```

```bash
curl http://cyphira.internal:8888
```

---

### gRPC Enumeration

```bash
grpcurl -plaintext \
cyphira.internal:6334 list
```

---

### Metadata Enumeration

```bash
curl \
http://cyphira.internal:5000/api/2.0/mlflow/registered-models/search
```

---

## Overall Risk Assessment

| Category | Risk |
|----------|------|
| AI Asset Discovery | High |
| Infrastructure Enumeration | High |
| Metadata Exposure | Medium |
| Administrative Interfaces | Medium |
| Model Intelligence Leakage | High |

---

# Key Findings

✔ Multiple AI platforms were successfully identified.

✔ AI framework fingerprinting confirmed enterprise ML infrastructure.

✔ Administrative metadata was accessible.

✔ AI development environments were externally discoverable.

✔ AI infrastructure significantly expanded the enterprise attack surface.

---

# Business Impact

The assessment demonstrates that AI environments introduce operational, architectural, and intelligence-related risks beyond those associated with traditional enterprise applications.

While no exploitation occurred during the engagement, exposed metadata and discoverable AI services could provide valuable reconnaissance information to a motivated adversary.

---

# Overall Conclusion

This project demonstrates a structured methodology for assessing enterprise AI infrastructure through reconnaissance, service fingerprinting, metadata analysis, and AI-specific risk evaluation.

By combining hands-on technical validation with recognized security frameworks—including MITRE ATT&CK, MITRE ATLAS, OWASP LLM Top 10, and the NIST AI RMF—the assessment illustrates how AI security extends beyond model protection to encompass the entire machine learning ecosystem.

The resulting documentation reflects an evidence-driven approach that emphasizes reproducibility, analyst validation, and actionable recommendations, mirroring the style of professional AI security consulting engagements.

---

# Disclaimer

All activities documented in this repository were performed within an **authorized laboratory and controlled training environment** for cybersecurity education and portfolio development. No unauthorized systems, production environments, or third-party infrastructure were targeted.
