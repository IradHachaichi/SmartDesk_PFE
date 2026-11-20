# AI Assistant — Controlled Natural-Language Client Information Retrieval

## 1. Objective

The AI Assistant makes structured client information accessible through natural-language questions. It targets operational questions about client contacts, roles, servers, URLs and related technical information.

Example questions:

- Who is the CDM of Client X?
- Which contact should be reached for a server incident?
- What is the production query server for this client?

The objective is not to build an unrestricted chatbot. It is to provide a controlled information-retrieval interface over a defined relational schema.

## 2. Technology Stack

| Component | Technology |
|---|---|
| Language | Python |
| REST API | FastAPI |
| Local LLM runtime | Ollama |
| Default model | Qwen 2.5 3B (`qwen2.5:3b`) |
| Database | PostgreSQL |
| Approximate matching | RapidFuzz |
| Schema validation | Pydantic |
| HTTP client | httpx |

## 3. Hybrid Processing Strategy

The assistant combines deterministic processing with LLM-based interpretation.

- Short greetings and selected supported cases can be handled deterministically.
- The LLM interprets requests when language understanding is required.
- Deterministic rules constrain or correct fields, filters and query construction.
- The backend validates the resulting query before execution.
- The response is generated from database results, not from unsupported model assumptions.

This design limits the amount of authority delegated to the language model.

## 4. Natural-Language-to-SQL Workflow

The high-level workflow is:

1. Receive a user question.
2. Inspect recent conversation context to identify a possible follow-up.
3. Classify the intent and identify the requested information.
4. Resolve the client and relevant entities.
5. Determine the required fields and conditions.
6. Construct SQL using deterministic rules where possible; use the model as an interpretation aid where needed.
7. Apply validation and defensive checks.
8. Execute an authorized read-only database query.
9. Format the returned records as a user-facing answer.

The exact processing path depends on the question family; not every request requires an LLM call.

## 5. Guardrails and Database Safety

The assistant follows a refusal-over-guessing principle.

Documented controls include:

- Read-only database access.
- Query validation before execution.
- Client-resolution and ambiguity checks.
- Protection against unrestricted database dumps when a required client filter cannot be resolved.
- Validation of requested field names and server families.
- Fail-closed handling when classification fails.
- Refusal when the requested information cannot be resolved reliably.

The LLM is not treated as an unrestricted SQL authority.

## 6. Conversational Follow-ups

Conversation context is used to resolve references such as “this client” when the preceding exchange established a client.

A newly named client is treated as a new explicit reference rather than automatically inheriting the previous client context. This helps avoid carrying an incorrect entity from one question to another.

## 7. Local Model Evaluation

The project compared compact models under a CPU-only local inference constraint.

The initial benchmark covered 20 questions and compared:

| Model | Model size | Global accuracy | Field selection | Median latency |
|---|---:|---:|---:|---:|
| Qwen 2.5 3B | 1.9 GB | 90% | 100% | 22 s |
| Qwen 2.5 Coder 1.5B | 1.0 GB | 90% | 93% | 10 s |
| Gemma 3 4B | 3.3 GB | 90% | 93% | 32 s |
| Phi-4 Mini | 2.5 GB | 80% | 71% | 50 s |
| Gemma 2 2B | 1.6 GB | 55% | 86% | 35 s |

The extended evaluation compared the two finalists on 50 questions:

| Model | Global accuracy | Field selection | Valid SQL | Result correctness | Median latency |
|---|---:|---:|---:|---:|---:|
| Qwen 2.5 3B | 92.2% | 94.6% | 97.7% | 100% | 24 s |
| Qwen 2.5 Coder 1.5B | 86.3% | 97.3% | 91.3% | 90.5% | 11 s |

Qwen 2.5 3B was selected as the default model because of its overall accuracy, result correctness and stability across runs. The smaller Coder model offered lower latency and higher field-selection performance in the reported comparison, making it a possible fast-mode alternative.

These metrics are tied to the project's evaluation set and definitions. They should not be interpreted as general-purpose LLM benchmarks.

## 8. Testing and Reported Results

The project documentation reports:

- 212 passing unit tests.
- 98% intent classification.
- 100% client resolution in the evaluated cases.
- 97.7% SQL validity under the documented `EXPLAIN` acceptance check.
- 92.2% global accuracy on the extended 50-question set.

The evaluation distinguishes query validity from answer correctness: a syntactically accepted query does not by itself prove that the returned answer is correct.

## 9. Limitations

- Evaluation sets are finite and project-specific.
- CPU-only local inference introduces latency constraints.
- Performance depends on the question distribution, schema and data quality.
- Unsupported or ambiguous requests should be refused.
- Results do not establish generalization to unseen client databases or unrelated domains.

## 10. Public Documentation Boundary

This repository intentionally excludes the proprietary implementation, database exports, real client records, credentials, internal endpoints and operational configuration. Screenshots must remain anonymized.
