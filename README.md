# Enterprise Automation Architecture

This repository outlines the architectural design and system logic for three enterprise-grade automation pipelines. These systems were built to solve critical operational bottlenecks across Pharmacy Operations, Accounts Payable, and Human Resources. 

Due to the proprietary nature of the code and the highly sensitive healthcare/financial data involved, the source code is kept in a private repository. The case studies below detail the architectural constraints, engineering methodologies, and commercial impact of each system.

---

## Case Study 1: The Autonomous RPA Claims Processor

### The Business Bottleneck
The operations team was crippled by the manual processing of thousands of pharmacy prescriptions and insurance claims. The process required a full-time claims department to manually read prescriptions, cross-reference patient data, and click through a complex desktop application to submit claims, resulting in high labor costs and human error.

### The Architectural Constraints
The target healthcare application was a heavily secured, closed-system thick client containing highly sensitive patient data. Reverse-engineering private APIs or intercepting network traffic was legally and technically prohibitive due to strict healthcare compliance regulations. The UI was inherently dynamic, prone to unannounced rendering changes, and lacked traditional DOM accessibility.

### The Engineering Solution
Designed and deployed an 11,000-line headless RPA engine in Python to operate the application programmatically.
* **Multimodal UI Fallbacks:** Bypassed brittle pixel-based coordinates by combining `PyAutoGUI` and `Tesseract OCR` with a custom multimodal LLM fallback (`Gemini Vision`). When standard OCR failed on dynamic UI elements, the system seamlessly routed targeted screen captures to the vision model to interpret the application's state.
* **Defensive State Ledgers:** Engineered a fault-tolerant state machine to manage execution. Implemented asynchronous exception-routing and `MAX_TRANSIENT_DEFER_ATTEMPTS` logic. If a UI row was unreadable, the system deferred the transaction to a cooldown queue rather than crashing, guaranteeing continuous execution.
* **Transactional Idempotency:** Maintained persistent, on-disk history ledgers to ensure that sudden system reboots or transient crashes would never result in duplicate claim submissions.

### The Commercial Impact
Achieved 24/7 fault-tolerant operation with zero data loss. The system effectively replaced the manual workload of an entire claims processing department, eradicating the operational bottleneck and drastically accelerating claim-to-revenue cycles.

---

## Case Study 2: Headless Accounts Payable Pipeline

### The Business Bottleneck
The Accounts Payable department was bogged down by manual triage. Staff had to manually monitor email inboxes, download vendor invoice PDFs, parse the financial data, and manually draft corresponding entries into the corporate accounting software (Xero).

### The Architectural Constraints
The system needed to interface securely with enterprise email servers without human intervention, parse highly variable vendor PDF structures, and guarantee that network timeouts during API synchronization did not create duplicate accounting entries.

### The Engineering Solution
Architected a headless data ingestion pipeline bridging Google Workspace and the Xero API.
* **OAuth-Driven Polling:** Deployed a background worker that authenticates via OAuth to poll targeted Gmail labels for incoming vendor communications.
* **Automated Extraction:** Programmatically detaches payloads, executes PDF data extraction, and sanitizes the output into standardized JSON objects.
* **Lockfiles & Ledger Tracking:** Implemented robust session management utilizing `.lock` files and historical tracking ledgers. The system verifies the state of each invoice against local state files before pushing the payload via the Xero API, ensuring strict idempotency.

### The Commercial Impact
Achieved zero-touch draft invoice generation. By entirely eliminating manual email triage and data entry, the pipeline freed the Accounts Payable team to focus exclusively on financial approval and strategy, removing a major administrative bottleneck.

---

## Case Study 3: Statutory UK Payroll & Gross-to-Net Engine

### The Business Bottleneck
End-of-month payroll reconciliation required hours of manual cross-referencing between HR attendance exports (BrightHR) and accounting endpoints (Xero). This manual bridging created a massive administrative burden and introduced the risk of calculation errors, which could trigger severe HMRC compliance penalties.

### The Architectural Constraints
Vendor attendance exports provided malformed, non-standard CSVs (e.g., unquoted commas merging into description fields) that broke standard data-parsing libraries. Additionally, the system had to dynamically calculate complex Gross-to-Net logic based on shifting period boundaries and hardcoded UK statutory rules.

### The Engineering Solution
Built a localized financial ingestion layer to serve as the integration bridge between the HR and Accounting systems.
* **Custom ETL Parsers:** Engineered robust Python data sanitization scripts designed specifically to catch and repair malformed vendor CSV exports before they entered the calculation pipeline.
* **Statutory Rule Engine:** Developed a dynamic calculation engine that processes Gross-to-Net logic. The system tracks dynamic period boundaries, maps tracking options, applies age-driven metrics, and calculates against a hardcoded index of statutory England & Wales Bank Holidays.
* **API Payload Construction:** Automatically formats the sanitized and calculated data into compliant payloads for the Xero Payroll API.

### The Commercial Impact
Delivered a zero-touch end-of-month payroll reconciliation process. The system eradicated human calculation errors, guaranteed strict adherence to HMRC regulations, and saved the HR department critical administrative hours during every pay cycle.
