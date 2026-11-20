# SmartDesk — System Architecture

## 1. Architectural Overview

SmartDesk is organized around one operational source, Jira, and three complementary modules:

1. AI Assistant for client-information retrieval.
2. Actuarial modelling for SLA breach risk quantification.
3. Power BI reporting for operational and risk decision support.

The modules are loosely coupled. They are independently executable and validated, and exchange information through files or database tables rather than direct module-to-module calls.

## 2. Source Data and ETL Chains

Jira provides two distinct exports.

### 2.1 Client-record export

The client-record export contains client attributes and free-text information such as contacts, infrastructure details, servers and URLs.

Its ETL chain:

- Converts the spreadsheet export into intermediate JSON.
- Cleans and normalizes values.
- Extracts URLs and environment metadata.
- Structures contacts and server information.
- Associates reporters with people records.
- Loads the resulting entities into PostgreSQL.

A local LLM assists with selected unstructured fields. Deterministic processing is used where formats are sufficiently predictable, and a fallback preserves raw values when generated JSON cannot be parsed.

### 2.2 SLA-ticket export

A separate export contains ticket-level SLA information.

Its preparation chain reconstructs and qualifies ticket events, including processing durations, contractual thresholds and breach status. The resulting dataset supports the actuarial model and reporting layer.

## 3. Module Boundaries

| Module | Input | Processing | Output |
|---|---|---|---|
| AI Assistant | Client-record export / PostgreSQL | Natural-language interpretation, controlled query construction and validation | Natural-language answers |
| Actuarial model | Prepared SLA-ticket data | Frequency, severity, calibration and simulation | Client- and portfolio-level risk outputs |
| Power BI | SLA data and actuarial outputs | Dimensional reporting and DAX measures | Interactive dashboards |

## 4. Application Integration

The React application provides a unified user interface.

- The AI Assistant is consumed through a REST API implemented with FastAPI.
- The Power BI report is integrated through an iframe using Microsoft's Secure Embed mechanism.
- The report's five pages are exposed through the application's reporting navigation.

The React application was developed by the project team. The individual contribution described in this repository focuses on the assistant and reporting integration, alongside the data and actuarial components.

## 5. Local and External Components

The client-data preparation, PostgreSQL storage, local LLM inference and Python actuarial processing were designed for local execution.

Power BI integration is different: Secure Embed references a report published in Power BI Service. The viewer authenticates through Microsoft and must have the required Power BI access. Consequently, the overall architecture is local-first for processing, but not fully offline.

## 6. Design Rationale

- **Separation of concerns:** each module addresses a distinct business problem.
- **Loose coupling:** data exchange through files and tables limits dependencies between modules.
- **Single source of truth for calculations:** statistical modelling is performed upstream; Power BI focuses on presentation and aggregation.
- **Controlled AI:** deterministic logic and validation constrain the LLM's role.
- **Traceability:** intermediate pipeline outputs and evaluation artefacts support inspection of transformations.

## 7. End-to-End Business Flow

The solution follows the sequence:

**Access → Quantify → Decide**

1. Retrieve client information through the assistant.
2. Estimate SLA breach exposure from prepared ticket history.
3. Present operational indicators and actuarial results through Power BI.
4. Use the combined information to support operational prioritization.

