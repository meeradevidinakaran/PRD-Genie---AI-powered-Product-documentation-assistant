# 🧞‍♂️ PRD Genie: AI-Powered Product Documentation Assistant

[![Version](https://img.shields.io/badge/version-v2.0--Production--Ready-blue.svg)](https://github.com/neuronforge/prd-genie)
[![Orchestration](https://img.shields.io/badge/Orchestration-Langflow-orange.svg)](https://github.com/langflow-ai/langflow)
[![Models](https://img.shields.io/badge/Models-OpenAI%20GPT--4o%20%7C%20GPT--4o--mini-green.svg)](https://openai.com/)
[![Observability](https://img.shields.io/badge/Observability-Langfuse-purple.svg)](https://langfuse.com/)
[![Integration](https://img.shields.io/badge/Database-Notion%20API%20v4-black.svg)](https://developers.notion.com/)
[![License](https://img.shields.io/badge/license-MIT-lightgray.svg)](LICENSE)

> **Autonomous multi-agent pipeline converting messy meeting transcripts, stakeholder notes, and product briefs into engineering-ready PRDs, persona-based Agile backlogs, and stakeholder gap reports in under 2 minutes.**

---

## 📌 Table of Contents
- [Executive Summary](#-executive-summary)
  
- [Problem Statement & Business Impact](#-problem-statement--business-impact)
  
- [System Architecture](#-system-architecture)
  
- [Key Features & Capabilities](#-key-features--capabilities)
  
- [Multi-Agent Pipeline & Routing Logic](#-multi-agent-pipeline--routing-logic)
  
- [Tech Stack & Model Tiering Strategy](#-tech-stack--model-tiering-strategy)
  
- [Performance & Business ROI (KPIs)](#-performance--business-roi-kpis)
  
- [Quick Start & Setup Guide](#-quick-start--setup-guide)
  
- [Environment Variables Configuration](#-environment-variables-configuration)
  
---

## Executive Summary

**Project PRD Genie** is an enterprise-grade agentic workflow developed for **NeuronForge Technologies**. It solves the universal product development bottleneck: manual, slow, and inconsistent translation of unstructured meeting notes into actionable technical documentation.

PRD Genie ingests raw text inputs from Notion, runs static logical extraction, routes inputs deterministically through high-reasoning LLMs, audits story-to-PRD alignment via an independent `LLM-as-a-Judge` agent, and automatically publishes standardized PRDs, MoSCoW-prioritized backlog stories, or Gap Clarification Reports directly into target Notion databases.

---

## 🔴 Problem Statement & Business Impact

Internal assessments at NeuronForge revealed critical software development lifecycle (SDLC) bottlenecks:
1. **High Manual Overhead:** Product Managers (PMs) and Technical Program Managers (TPMs) spend **8+ hours per document** manually digesting notes, writing PRDs, and creating Agile user stories.
2. **Format Inconsistency:** Varied document structures across PMs lead to downstream estimation friction for engineering leads.
3. **Information Leakage:** Vital requirements discussed in meetings remain trapped in transcripts and local files.
4. **AI Scope Fabrication Risks:** Naive single-prompt AI tools often hallucinate non-existent features or alter numerical technical specifications (e.g., rounding `200ms at p95` to `200ms`).

### 🎯 Business Solution & Impact
* **90% Productivity Boost:** Drops Time-to-PRD from **8+ hours to < 30 minutes** (pipeline execution `< 120s`).
* **100% Structural Standardization:** Enforces uniform, engineering-approved PRD and Agile story schemas.
* **Zero-Hallucination SLA:** Strict prompt guardrails enforce verbatim capture of numerical metrics and technical boundaries.

---

## 🏗️ System Architecture 

PRD Genie employs a decoupled 5-tier architecture spanning Ingress, Deterministic Routing, Multi-Agent Generation, Automated Quality Auditing, and Bidirectional Database Sync.

### High-Level Design (HLD)
<img width="1917" height="845" alt="HLD_V2" src="https://github.com/user-attachments/assets/7461032e-27f1-425e-8c52-c49a8df370fd" />

## Logical Workflow 
<img width="911" height="622" alt="Screenshot 2026-09-13 122107" src="https://github.com/user-attachments/assets/ea3c28f5-ced7-4ead-8410-ece08f6b8f15" />


## Workflow (Langflow Snapshot)

<img width="1917" height="953" alt="Screenshot 2026-09-12 223837" src="https://github.com/user-attachments/assets/fb2177d5-dc88-4233-9dee-9bb97ac94e27" />


---

## ✨ Key Features & Capabilities

1. **Automated Background Ingestion (`Notion Source Poller V4`):** Polls Notion workspace databases for `Pending` rows, extracting raw meeting transcripts, documents, and row metadata without manual file uploading.
2. **Schema-Validated Requirement Extractor (`Node 1`):** Ingests raw text and performs static analysis to extract metadata, functional requirements, non-functional requirements (NFRs), stakeholders, deadlines, unstated assumptions, and explicit logical contradictions.
3. **1-Wire Pipeline Decision Gate (`Decision Gate V4`):** Pure Python custom component that inspects top-level and nested `is_ambiguous` requirement flags and `contradiction_detected` objects, prepending `__ROUTE_DECISION: ROUTE_TO_PRD__` (or `ROUTE_TO_GAP`) to pass payload and routing tag through a single wire.
4. **Zero-Hallucination PRD Generator (`Node 2`):** Merges extracted requirements into a standardized Markdown PRD template covering Overview, Goals, Personas, Features, MoSCoW Priorities, NFRs, and Open Questions.
5. **Persona-Segmented Story Breakdown (`Node 3`):** Deconstructs PRD features into granular Agile user stories formatted in standard `As a [Persona], I want [action] so that [benefit]` syntax with Given-When-Then acceptance criteria checklist items.
6. **Automated Semantic Alignment Judge (`Node 3.5`):** Independent LLM-as-a-Judge node that audits story-to-PRD coverage, checks for scope drift ("phantom stories"), and outputs a quantitative **Alignment Score (%)** and audit remarks.
7. **Extended Gap Analyzer (`Node 2b`):** Triggers when ambiguous or contradictory inputs are detected. Halts PRD creation and outputs a structured Gap Report with **Readiness Status (RED/YELLOW/GREEN)** and role-grouped clarification questions (PM, Engineering, UX, Sales).
8. **Bidirectional State Synchronization (`Notion Status Updater V4`):** Automatically updates Notion Ingestion Queue row status from `Pending` to **`Processed`** (Route A) or **`Gap Identified`** (Route B) with page URL references.

---

## ⚡ Tech Stack & Model Tiering Strategy

| Layer | Technology / Tool | Selection Rationale |
| :--- | :--- | :--- |
| **Workflow Engine** | **Langflow 1.0+** | Rapid visual prototyping of sequential multi-agent pipelines with exportable JSON configuration management. |
| **Lightweight LLM** | **OpenAI GPT-4o-mini** | Low-cost, low-latency extraction (Node 1) and gap reporting (Node 2b). Handles 70%+ of total token volume. |
| **High-Reasoning LLM**| **OpenAI GPT-4o** | Reserved strictly for high-fidelity PRD compilation (Node 2) and Agile user story breakdown (Node 3). |
| **Judge Model** | **Meta Llama 3.3 70B / OpenRouter** | Independent model used in Node 3.5 to prevent self-preference bias during story alignment evaluation. |
| **Observability** | **Langfuse** | Native nested execution tracing, tracking latency, token usage, and cost per node. |
| **Integration / Sinks**| **Notion API v4 (Custom Python)** | Workspace collaboration hub for PM/Engineering handoffs. |

### 💰 Cost Optimization (Multi-LLM Tiering)
* **Token Budgeting:** GPT-4o-mini processes extraction for ~$0.0012/run. GPT-4o handles synthesis for ~$0.040/run.
* **Average Cost per Pipeline Run:** **$0.045 – $0.050 USD** (saves ~75% compared to single-model GPT-4o pipelines @ $0.18/run).

---

## 📊 Performance & Business ROI (KPIs)

| Metric | Target SLA | Verified Result (12-Input Test Dataset) | Status |
| :--- | :--- | :--- | :---: |
| **Time-to-PRD** | < 30 minutes | **< 2 minutes end-to-end execution** | 🟢 PASS |
| **Template Adherence** | 100% | **100% compliance** with standard PRD schema | 🟢 PASS |
| **Data Grounding SLA** | 100% (0% Hallucination) | **100% grounded**; 0 invented features | 🟢 PASS |
| **Verbatim Technical Spec SLA**| 100% exact retention | **100% verbatim capture** (no metric rounding) | 🟢 PASS |
| **Decision Gate Accuracy** | 100% deterministic | **100% routing accuracy** across test suite | 🟢 PASS |
| **Story Alignment Score** | $\ge$ 90% average | **95.8% average alignment score** | 🟢 PASS |

---
## DEMO Video link

⏺️ https://drive.google.com/file/d/1twO1ulWr0ztQ0yXWDRb4jZpM8uBDBJ4X/view?usp=drive_link

---
## 🚀 Quick Start & Setup Guide
```
### Prerequisites
* **Python:** Version 3.10, 3.11, or 3.12
* **Langflow:** Version 1.0.0 or higher (`pip install langflow`)
* **API Keys:**
  * OpenAI API Key (`sk-...`)
  * Notion Integration Secret Token (`secret_...`)
  * Langfuse Public & Secret Keys (Optional for tracing)


### Step 1: Install Dependencies
### Step 2: Configure Environment Variables
### Step 3: Set Up Notion Workspace Databases
Create three target databases in your Notion workspace and share them with your Notion Integration:
1. **Ingestion Queue DB:** Properties: `Title` (Title), `Transcript` (Text), `Processing Status` (Text or Select: `Pending`, `Processed`).
2. **Engineering Backlog DB:** Output database for PRDs and Agile stories.
3. **Stakeholder Alignment DB:** Output database for Gap Reports.
### Step 4: Launch Langflow & Import Flow

## ⚙️ Environment Variables 

# OpenAI API Credentials
OPENAI_API_KEY=sk-proj-xxxxxxxxxxxxxxxxxxxxxxxx

# Notion API Integration Credentials
NOTION_SECRET_TOKEN=secret_xxxxxxxxxxxxxxxxxxxxxxxx

# Langfuse Observability Tracing Credentials
LANGFUSE_PUBLIC_KEY=pk-lf-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
LANGFUSE_SECRET_KEY=sk-lf-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
LANGFUSE_HOST=https://cloud.langfuse.com
```

---

<p align="center">
  <i>Built using Langflow, OpenAI, and Notion API for NeuronForge Technologies.</i>
</p>
