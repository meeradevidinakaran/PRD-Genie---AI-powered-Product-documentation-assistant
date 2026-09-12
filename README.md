🧞‍♂️ PRD Genie: AI-Powered Product Documentation Assistant
--Image of: --Version --Image of: --Orchestration --Image of: --Models --Image of: --Observability --Image of: --Integration --Image of: --License

Autonomous multi-agent pipeline converting messy meeting transcripts, stakeholder notes, and product briefs into engineering-ready PRDs, persona-based Agile backlogs, and stakeholder gap reports in under 2 minutes.

📌 Table of Contents
Executive Summary
Problem Statement & Business Impact
System Architecture (HLD & LLD)
Key Features & Capabilities
Multi-Agent Pipeline & Routing Logic
Tech Stack & Model Tiering Strategy
Performance & Business ROI (KPIs)
Repository Structure
Quick Start & Setup Guide
Environment Variables Configuration
License & Attribution
Executive Summary
Project PRD Genie is an enterprise-grade agentic workflow developed for NeuronForge Technologies. It solves the universal product development bottleneck: manual, slow, and inconsistent translation of unstructured meeting notes into actionable technical documentation.

PRD Genie ingests raw text inputs from Notion, runs static logical extraction, routes inputs deterministically through high-reasoning LLMs, audits story-to-PRD alignment via an independent LLM-as-a-Judge agent, and automatically publishes standardized PRDs, MoSCoW-prioritized backlog stories, or Gap Clarification Reports directly into target Notion databases.

🔴 Problem Statement & Business Impact
Internal assessments at NeuronForge revealed critical software development lifecycle (SDLC) bottlenecks:

High Manual Overhead: Product Managers (PMs) and Technical Program Managers (TPMs) spend 8+ hours per document manually digesting notes, writing PRDs, and creating Agile user stories.
Format Inconsistency: Varied document structures across PMs lead to downstream estimation friction for engineering leads.
Information Leakage: Vital requirements discussed in meetings remain trapped in transcripts and local files.
AI Scope Fabrication Risks: Naive single-prompt AI tools often hallucinate non-existent features or alter numerical technical specifications (e.g., rounding 200ms at p95 to 200ms).
🎯 Business Solution & Impact
90% Productivity Boost: Drops Time-to-PRD from 8+ hours to < 30 minutes (pipeline execution < 120s).
100% Structural Standardization: Enforces uniform, engineering-approved PRD and Agile story schemas.
Zero-Hallucination SLA: Strict prompt guardrails enforce verbatim capture of numerical metrics and technical boundaries.
🏗️ System Architecture (HLD & LLD)
PRD Genie employs a decoupled 5-tier architecture spanning Ingress, Deterministic Routing, Multi-Agent Generation, Automated Quality Auditing, and Bidirectional Database Sync.

High-Level Design (HLD)
[ Notion Ingestion Queue DB ]
             │ (Row Status: "Pending")
             ▼
 [ Notion Source Poller V4 ] ──► [ Payload Transformer ]
                                          │
                                          ▼
                         [ Node 1: Requirement Extractor ] (GPT-4o-mini)
                                          │
                                          ▼
                      [ Node 1.5: Pipeline Decision Gate V4 ]
                                          │
                  ┌───────────────────────┴───────────────────────┐
                  ▼ (Route A: Clean)                              ▼ (Route B: Ambiguous/Contradiction)
        [ Node 2: PRD Generator ]                       [ Node 2b: Gap Analyzer ] (GPT-4o-mini)
               (GPT-4o)                                           │
                  │                                               ▼
                  ▼                                   [ Notion Gap Report Writer ]
       [ Node 3: Story Breakdown ]                                │
               (GPT-4o)                                           ▼
                  │                                  [ Notion Status Updater V4 ]
                  ├───────────────────────┐             (Status: "Gap Identified")
                  ▼                       │                       │
     [ Node 3.5: LLM-as-a-Judge ] ◄───────┘                       ▼
     (Alignment Score & Audit %)                     [ Notion Stakeholder Alignment DB ]
                  │
                  ▼
   [ Notion PRD & Story Writer ]
                  │
                  ▼
     [ Notion Status Updater V4 ] (Status: "Processed")
                  │
                  ▼
  [ Notion Engineering Backlog DB ]
Low-Level Execution Sequence (Mermaid LLD)
graph TD
    subgraph Ingestion["1. Ingress & Payload Isolation"]
        Poller["Notion Source Poller V4"] --> Transformer["Payload Transformer (Custom Component)"]
    end

    subgraph Extraction_Routing["2. Extraction & Deterministic Routing"]
        Transformer --> Extractor["Requirement Extractor Agent (GPT-4o-mini)"]
        Extractor --> Gate["Pipeline Decision Gate V4 (Python Rule Engine)"]
        Gate --> Router{"If-Else Router (Contains: ROUTE_TO_PRD)"}
    end

    subgraph Route_A["3. Route A: PRD Synthesis & Quality Audit"]
        Router -- "True" --> PRDGen["PRD Generator Agent (GPT-4o)"]
        PRDGen --> StoryAgent["Story Breakdown Agent (GPT-4o)"]
        PRDGen -. "PRD Payload" .-> Judge["LLM-as-a-Judge Node (Semantic Audit %)"]
        StoryAgent -. "User Stories" .-> Judge
        Judge --> PRDWriter["Notion PRD & Story Writer"]
        PRDWriter --> StatusA["Notion Status Updater V4 (Status: Processed)"]
        StatusA --> EngDB[("Notion Engineering Backlog DB")]
    end

    subgraph Route_B["4. Route B: Gap Analysis & Clarification"]
        Router -- "False" --> GapAgent["Gap Analyzer Agent (GPT-4o-mini)"]
        GapAgent --> GapWriter["Notion Gap Report Writer"]
        GapWriter --> StatusB["Notion Status Updater V4 (Status: Gap Identified)"]
        StatusB --> StakeDB[("Notion Stakeholder Alignment DB")]
    end

    subgraph Observability["5. Telemetry & Control"]
        Extractor -. Traces .-> Langfuse[[Langfuse Observability Engine]]
        PRDGen -. Traces .-> Langfuse
        StoryAgent -. Traces .-> Langfuse
        Judge -. Traces .-> Langfuse
        GapAgent -. Traces .-> Langfuse
    end

    classDef decision fill:#fff3cd,stroke:#ffc107,stroke-width:2px;
    classDef routeA fill:#e8f5e9,stroke:#2e7d32,stroke-width:1.5px;
    classDef routeB fill:#ffebee,stroke:#c62828,stroke-width:1.5px;
    class Router decision;
    class PRDGen,StoryAgent,PRDWriter,EngDB routeA;
    class GapAgent,GapWriter,StakeDB routeB;
✨ Key Features & Capabilities
Automated Background Ingestion (Notion Source Poller V4): Polls Notion workspace databases for Pending rows, extracting raw meeting transcripts, documents, and row metadata without manual file uploading.
Schema-Validated Requirement Extractor (Node 1): Ingests raw text and performs static analysis to extract metadata, functional requirements, non-functional requirements (NFRs), stakeholders, deadlines, unstated assumptions, and explicit logical contradictions.
1-Wire Pipeline Decision Gate (Decision Gate V4): Pure Python custom component that inspects top-level and nested is_ambiguous requirement flags and contradiction_detected objects, prepending __ROUTE_DECISION: ROUTE_TO_PRD__ (or ROUTE_TO_GAP) to pass payload and routing tag through a single wire.
Zero-Hallucination PRD Generator (Node 2): Merges extracted requirements into a standardized Markdown PRD template covering Overview, Goals, Personas, Features, MoSCoW Priorities, NFRs, and Open Questions.
Persona-Segmented Story Breakdown (Node 3): Deconstructs PRD features into granular Agile user stories formatted in standard As a [Persona], I want [action] so that [benefit] syntax with Given-When-Then acceptance criteria checklist items.
Automated Semantic Alignment Judge (Node 3.5): Independent LLM-as-a-Judge node that audits story-to-PRD coverage, checks for scope drift ("phantom stories"), and outputs a quantitative Alignment Score (%) and audit remarks.
Extended Gap Analyzer (Node 2b): Triggers when ambiguous or contradictory inputs are detected. Halts PRD creation and outputs a structured Gap Report with Readiness Status (RED/YELLOW/GREEN) and role-grouped clarification questions (PM, Engineering, UX, Sales).
Bidirectional State Synchronization (Notion Status Updater V4): Automatically updates Notion Ingestion Queue row status from Pending to Processed (Route A) or Gap Identified (Route B) with page URL references.
⚡ Tech Stack & Model Tiering Strategy
Layer	Technology / Tool	Selection Rationale
Workflow Engine	Langflow 1.0+	Rapid visual prototyping of sequential multi-agent pipelines with exportable JSON configuration management.
Lightweight LLM	OpenAI GPT-4o-mini	Low-cost, low-latency extraction (Node 1) and gap reporting (Node 2b). Handles 70%+ of total token volume.
High-Reasoning LLM	OpenAI GPT-4o	Reserved strictly for high-fidelity PRD compilation (Node 2) and Agile user story breakdown (Node 3).
Judge Model	Meta Llama 3.3 70B / OpenRouter	Independent model used in Node 3.5 to prevent self-preference bias during story alignment evaluation.
Observability	Langfuse	Native nested execution tracing, tracking latency, token usage, and cost per node.
Integration / Sinks	Notion API v4 (Custom Python)	Workspace collaboration hub for PM/Engineering handoffs.
💰 Cost Optimization (Multi-LLM Tiering)
Token Budgeting: GPT-4o-mini processes extraction for ~$0.0012/run. GPT-4o handles synthesis for ~$0.040/run.
Average Cost per Pipeline Run: $0.045 – $0.050 USD (saves ~75% compared to single-model GPT-4o pipelines @ $0.18/run).
📊 Performance & Business ROI (KPIs)
Metric	Target SLA	Verified Result (12-Input Test Dataset)	Status
Time-to-PRD	< 30 minutes	< 2 minutes end-to-end execution	🟢 PASS
Template Adherence	100%	100% compliance with standard PRD schema	🟢 PASS
Data Grounding SLA	100% (0% Hallucination)	100% grounded; 0 invented features	🟢 PASS
Verbatim Technical Spec SLA	100% exact retention	100% verbatim capture (no metric rounding)	🟢 PASS
Decision Gate Accuracy	100% deterministic	100% routing accuracy across test suite	🟢 PASS
Story Alignment Score	$\ge$ 90% average	95.8% average alignment score	🟢 PASS
Verification Dataset: Full baseline evaluation logs are published in the PRD Genie QA Test Suite Spreadsheet.

📁 Repository Structure
prd-genie/
├── README.md                              <- Primary Documentation & Setup Guide
├── LICENSE                                <- MIT License
├── requirements.txt                       <- Python dependencies for custom components
├── .env.example                           <- Sample environment variables config
│
├── workflows/                             <- Exported Langflow Pipeline JSONs
│   ├── prd_genie_v2_final.json            <- Full Production Pipeline (v2.0)
│   ├── prd_genie_v1.1_observability.json  <- Observability & Routing PoC (v1.1)
│   └── prd_genie_v1.0_basic.json          <- Basic Sequential Pipeline (v1.0)
│
├── custom_components/                     <- Standalone Python Custom Component Code
│   ├── notion_source_poller_v4.py         <- Poller Component for Ingestion Queue
│   ├── payload_transformer.py             <- Text/ID Extraction Splitter
│   ├── pipeline_decision_gate_v4.py       <- Deterministic Route Tagging Engine
│   ├── notion_prd_story_writer_v4.py      <- Custom Notion PRD & Story Blocks Writer
│   ├── notion_gap_report_writer_v5.py     <- Custom Notion Gap Analysis Writer
│   └── notion_status_updater_v4.py        <- Bidirectional Status Sync Component
│
├── prompts/                               <- System Prompt Library
│   ├── node1_requirement_extractor.md     <- Requirement Extractor System Prompt
│   ├── node2_prd_generator.md             <- PRD Generator System Prompt
│   ├── node2b_gap_analyzer.md             <- Gap Analyzer System Prompt
│   ├── node3_story_breakdown.md           <- Story Breakdown System Prompt
│   └── node3.5_judge_alignment.md         <- LLM-as-a-Judge System Prompt
│
├── docs/                                  <- Program Charter, HLD, LLD & Specs
│   ├── program_charter_v2.md              <- Executive Program Charter
│   ├── hld_architecture_spec.md           <- High-Level Design Specification
│   ├── lld_component_matrix.md            <- Low-Level Interface Specifications
│   └── prd_genie_product_spec.md          <- System Product Requirement Document
│
└── screenshots/                           <- Execution Flow & Notion Screenshots
    ├── langflow_canvas_v2.png             <- Complete V2 Pipeline Canvas
    ├── notion_ingestion_queue.png         <- Notion Ingestion DB Status Transition
    └── notion_generated_prd.png           <- Sample Published PRD & Backlog Page
🚀 Quick Start & Setup Guide
Prerequisites
Python: Version 3.10, 3.11, or 3.12
Langflow: Version 1.0.0 or higher (pip install langflow)
API Keys:
OpenAI API Key (sk-...)
Notion Integration Secret Token (secret_...)
Langfuse Public & Secret Keys (Optional for tracing)
Step 1: Clone the Repository
git clone https://github.com/neuronforge/prd-genie.git
cd prd-genie
Step 2: Install Dependencies
pip install -r requirements.txt
Step 3: Configure Environment Variables
Copy .env.example to .env and populate your secrets:

cp .env.example .env
Step 4: Set Up Notion Workspace Databases
Create three target databases in your Notion workspace and share them with your Notion Integration:

Ingestion Queue DB: Properties: Title (Title), Transcript (Text), Processing Status (Select: Pending, Processing, Processed, Gap Identified), Generated URL (URL).
Engineering Backlog DB: Output database for PRDs and Agile stories.
Stakeholder Alignment DB: Output database for Gap Reports.
Step 5: Launch Langflow & Import Flow
langflow run
Open your browser at http://localhost:7860.
Click Import and select workflows/prd_genie_v2_final.json.
In the canvas, enter your NOTION_SECRET_TOKEN and Database IDs into the custom component input fields.
Click Playground \(\rightarrow\) Run to trigger background polling and execution!
⚙️ Environment Variables Configuration
Sample .env.example:

# OpenAI API Credentials
OPENAI_API_KEY=sk-proj-xxxxxxxxxxxxxxxxxxxxxxxx

# Notion API Integration Credentials
NOTION_SECRET_TOKEN=secret_xxxxxxxxxxxxxxxxxxxxxxxx
NOTION_INGESTION_DB_ID=3a453335049e80048be4c1954c000000
NOTION_BACKLOG_DB_ID=3d053335049e809590960cc777300000
NOTION_GAP_DB_ID=3e153335049e809590960cc777400000

# Langfuse Observability Tracing Credentials
LANGFUSE_PUBLIC_KEY=pk-lf-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
LANGFUSE_SECRET_KEY=sk-lf-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
LANGFUSE_HOST=https://cloud.langfuse.com
📄 License & Attribution
Distributed under the MIT License. See LICENSE for details.

Author: Meeradevi Dinakaran (AI PM/TPM, Product and Innovation Team)
Organization: NeuronForge Technologies
Project: Capstone Program Charter — Project PRD Genie (v2.0)
