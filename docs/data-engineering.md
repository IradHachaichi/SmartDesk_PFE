# Data Engineering Pipeline — Jira Client Information

## 1. Objective

The client-information pipeline transforms a flat Jira spreadsheet export into a structured and queryable PostgreSQL database for the SmartDesk AI Assistant.

The source records combine structured attributes with free-text descriptions, contacts, infrastructure details, server references and URLs.

## 2. Source Data Challenges

The preparation process addresses:

- HTML entities such as `&lt;`, `&nbsp;` and `&amp;`.
- Inconsistent whitespace and non-breaking spaces.
- Multiple contacts stored in a single cell.
- Server information embedded in free-text fields.
- URLs and environment labels stored together.
- Client, people and infrastructure information stored in a flat structure.

## 3. ETL Stages

The processing workflow was consolidated into a Python orchestrator, `pipeline.py`, replacing approximately fifteen independently executed scripts.

The documented stages are:

1. **Spreadsheet to raw JSON:** convert the Jira spreadsheet export into an intermediate JSON representation.
2. **Cleaning and structuring:** decode HTML entities, normalize whitespace and structure predictable fields.
3. **URL extraction:** separate URLs, environments and subtypes into structured records.
4. **Contact extraction:** use a local LLM to structure contact information from free text.
5. **Database-server extraction:** identify database-server references and associated environment information.
6. **Query-server extraction:** structure Query server information.
7. **Reporter association:** connect the reporter of each client record to the people table.

Regular expressions are used when the source format is sufficiently predictable. Local LLM extraction is used for selected unstructured fields.

## 4. LLM Extraction and Fallback

The local model receives the original text and is instructed to return structured JSON.

The pipeline uses deterministic generation settings, including temperature zero. JSON parsing is validated. When the generated response cannot be parsed, the pipeline preserves the original raw value through a fallback structure instead of silently discarding it.

This approach combines flexible extraction with data-preservation safeguards.

## 5. Orchestration and Traceability

The orchestrator provides:

- Centralized configuration for paths, model settings and extraction fields.
- Reusable helpers for JSON I/O, model calls and tolerant JSON extraction.
- Explicit stage execution order.
- Numbered intermediate outputs.
- Individual traces of LLM calls.
- Command-line options to list stages, run the complete pipeline or resume from a selected stage.

## 6. Relational Database Structure

The documented output contains eight relational tables:

| Table | Records |
|---|---:|
| `clients` | 58 |
| `infos_jira` | 58 |
| `metriques_clients` | 58 |
| `client_groupes` | 53 |
| `client_serveurs` | 428 |
| `client_urls` | 243 |
| `personnes` | 319 |
| `roles_clients` | 570 |
| **Total** | **1,787** |

The `clients` table stores client-level information. Satellite tables contain additional attributes and infrastructure details and share the `jira_key` identifier.

People are stored separately in `personnes` and associated with clients through `roles_clients`.

These counts describe the project dataset. Retain them in the public repository only if their disclosure is permitted.

## 7. Technology Stack

Python; regular expressions; Ollama; Qwen; JSON; PostgreSQL.

## 8. Key Engineering Contributions

- Consolidation of a multi-stage ETL workflow.
- Combination of deterministic parsing and local LLM extraction.
- Fallback mechanisms to preserve raw information.
- Reproducible processing and intermediate artefacts.
- Relational normalization of flat client records.
- Preparation of a queryable data foundation for natural-language retrieval.

## 9. Public Data Boundary

No real Jira exports, client names, contacts, server names, URLs, credentials or internal endpoints are included in this repository.
