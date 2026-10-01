# Enterprise Automation Architecture

Designing fault-tolerant, AI-native systems that bridge legacy software gaps, enforce data integrity, and drive commercial efficiency.

This repository serves as a technical portfolio detailing the architecture of production-grade automation systems. These systems are engineered to operate in highly constrained, API-restricted environments, leveraging multimodal AI, robust anti-corruption layers, and strict state management to deliver deterministic business value.

## 1. Autonomous Enterprise RPA Engine

**The Business Context**
Deployed across a 50-branch enterprise network, this 11,000-line autonomous RPA system replaced a massive manual data-entry bottleneck. By completely automating this workflow, the system generates over £6,700/month in direct operational savings.

**The Architecture**
Operating within a locked-down, legacy thick-client application devoid of public APIs, the system bypasses traditional, brittle DOM interactions.
* **Multimodal UI Extraction:** Utilizes Google Gemini Vision to dynamically interpret and extract state from unreadable legacy UI elements.
* **Deterministic ReAct Loops:** Decision-making is governed by strictly bounded ReAct loops, ensuring the agent evaluates its visual state before executing the next payload.
* **Fault Tolerance:** Engineered with robust retry decorators (`MAX_TRANSIENT_DEFER_ATTEMPTS`) and localized JSON state ledgers. If the target application experiences rendering lag, the engine defers the transaction to a cooldown queue, guaranteeing system idempotency and zero data loss.

## 2. Anti-Corruption Layer (Statutory Payroll)

**The Business Context**
Engineered to guarantee strict United Kingdom statutory compliance within the Xero accounting backend. This system acts as a defensive shield, sanitizing severely broken upstream data from a legacy HR vendor and eliminating the need for manual payroll reconciliation.

**The Architecture**
A highly defensive ETL architecture designed to untangle malformed vendor exports.
* **Data Sanitization & Alias Resolution:** Programmatically resolved complex entity aliases in environments where 96% of upstream rows lacked reliable primary keys.
* **Overlap Prevention:** Implemented advanced temporal logic to prevent duplicate ledger counting across overlapping cutoff dates.
* **State-Backed Execution:** Architected with local SQLite state ledgers and strict exception routing. When standard data exports fail, the system falls back to automated browser-extraction to guarantee uninterrupted execution.

## 3. 24/7 Inbox-to-Xero Invoice Agent

**The Business Context**
Deployed during a critical operational capacity crisis, this autonomous agent absorbed the manual data entry workload of three full-time staff members, achieving zero-touch invoice processing for the Accounts Payable department.

**The Architecture**
A highly scalable, asynchronous ingestion pipeline executing end-to-end accounting automation.
* **Event-Driven Orchestration:** Triggered via Gmail API webhooks, the system intercepts incoming vendor communications in real-time.
* **Multimodal Extraction & Constraint Mapping:** Leverages Gemini Vision for multi-page, unstructured PDF extraction. The raw output is passed through a strict vendor constraint mapping layer to validate tax codes, line items, and totals.
* **Autonomous Injection:** Executes 24/7 payload injections directly into the Xero backend. The pipeline relies heavily on defensive code and persistent lockfiles to ensure transaction idempotency, eliminating the risk of duplicate draft invoices during network timeouts.

## Core Engineering Tenets

Across all deployments, these systems adhere to strict enterprise software engineering standards to survive hostile, undocumented environments:
1. **System Idempotency:** Utilizing localized SQLite and JSON state ledgers to ensure that sudden crashes or reboots never result in duplicate financial transactions.
2. **Defensive Fault Tolerance:** Extensive use of retry decorators, fallback queues, and exception routing to gracefully manage transient third-party failures.
3. **AI as an Architectural Bridge:** Leveraging multimodal LLMs not as generative toys, but as deterministic perceptual bridges to extract structured data from API-less legacy systems.
