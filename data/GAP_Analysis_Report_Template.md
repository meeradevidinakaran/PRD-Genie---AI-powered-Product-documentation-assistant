GAP Analysis Report Template

# REQUIREMENTS READINESS & GAP ANALYSIS REPORT: [Project Name / Feature Name]
**Evaluation Date:** September 3, 2026  
**Auditor Agent:** PRD Genie Gap Analyzer Node (v1.0)  

---

## 1. Executive Readiness Status
`[RED / YELLOW / GREEN]`  
*Provide a 2-3 sentence high-level summary of why this rating was assigned, detailing if the requirements are safe to pass to downstream PRD and Story engineering.*

---

## 2. Critical Contradictions & Structural Tensions
*List and describe any active conflicts between stakeholders or engineering constraints. For each conflict, include:*
*   **The Conflict:** (e.g., 5-second polling vs. Infrastructure load)
*   **Stakeholders Involved:** (e.g., Priya vs. Kevin & Mike)
*   **Technical Impact:** (e.g., Extreme risk of server outages and database performance degradation)
*   **Suggested Mediation / Raw Alternatives:** (e.g., Mike suggested WebSockets; needs offline resolution)

*If none, state: "No active contradictions detected."*

---

## 3. Scope Gaps & Omissions (Undefined Parameters)
*Identify critical product parameters that are missing or vague. For each gap, include:*
*   **Gap Category:** (e.g., Performance, Target Users, Success Metrics, Data Ingestion)
*   **Description:** (e.g., Feature requested is "AI Reporting", but no details on expected outputs, models, or data inputs are specified)
*   **Source Reference:** (e.g., "AI Ideas Sync Meeting - Tom")

*If none, state: "No critical omissions detected."*

---

## 4. Technical Risks & Dependencies
*Map out immediate program execution risks and external blockers:*
*   **Database Scaling Risk:** (e.g., Raj notes full table scans take 8+ seconds; events table lacks an index on timestamp column)
*   **Architecture Realignment:** (e.g., Shifting analytics to a dedicated microservice instead of bolting onto the monolith)
*   **Timeline Feasibility Risk:** (e.g., VP Sales demanding delivery by end of March vs. Raj's estimate of 2-3 sprints for backend)
*   **Multi-Tenancy Security:** (e.g., Nina's note that multi-tenant support is critical; risk of data leakage between accounts)

---

## 5. TPM Resolution Gate (Targeted Clarification Questions)
*Provide the exact questions that the PM/TPM must answer to resolve the RED/YELLOW states before V1.0 PRD compilation:*

### 📋 Questions for Product Management (PM)
1. *[Question]*
2. *[Question]*

### 🛠️ Questions for Engineering & DevOps
1. *[Question]*

### 🎨 Questions for UX/UI Design
1. *[Question]*

### 💼 Questions for Business / Sales
1. *[Question]*