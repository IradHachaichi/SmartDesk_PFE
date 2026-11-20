# Public Repository — Confidentiality and Disclosure Checklist

This checklist should be completed before making the repository public and again whenever screenshots or documentation are updated.

## 1. Authorization

- [ ] Confirm that the internship agreement and organization policies allow public description of the project.
- [ ] Obtain approval before publishing company names, project context, internal architecture or measured results where required.
- [ ] Do not assume that anonymizing client names automatically makes every metric publishable.

## 2. Data and Screenshots

- [ ] No real client names, identifiers or recognizable combinations of attributes.
- [ ] No personal names, email addresses, phone numbers or assignee identifiers.
- [ ] No production server names, hostnames, IP addresses or internal URLs.
- [ ] No Jira issue keys, internal project identifiers or confidential ticket descriptions.
- [ ] No credentials, tokens, API keys, connection strings or secrets.
- [ ] No Power BI tenant IDs, report IDs or unrestricted embed links.
- [ ] No screenshots showing browser tabs, account names, notifications or unrelated internal systems.

## 3. Numerical Results

- [ ] Confirm that dataset sizes and table counts may be disclosed.
- [ ] Confirm that AI evaluation metrics may be disclosed.
- [ ] Do not publish actual client-level or portfolio actuarial values without authorization.
- [ ] Do not publish contractual penalty amounts or client-specific SLA thresholds.
- [ ] Clearly distinguish project evaluation results from production guarantees.

## 4. Technical Accuracy

- [ ] Describe Power BI Service as an external dependency.
- [ ] Do not describe the entire architecture as fully offline or 100% local.
- [ ] Distinguish personal contributions from team-developed components.
- [ ] Explain that model results depend on assumptions and historical data.
- [ ] Keep the distinction between SQL validity and answer correctness.

## 5. Repository Hygiene

- [ ] Search all files for `password`, `token`, `secret`, `api_key`, `localhost`, internal domains and client identifiers.
- [ ] Inspect image metadata and screenshot contents.
- [ ] Check Git history for accidentally committed secrets or data.
- [ ] Add an appropriate `.gitignore`.
- [ ] Do not upload the original PFE report unless publication is explicitly authorized.
- [ ] Do not add a permissive open-source licence to proprietary code or material without permission.

## 6. Suggested `.gitignore`

```gitignore
.env
.env.*
!.env.example
__pycache__/
*.py[cod]
.venv/
venv/
.DS_Store
Thumbs.db
*.log
*.sqlite
*.db
data/
exports/
secrets/
credentials/
```

This is a starting point only. Adapt it to the actual repository contents.

## 7. Important Note

This checklist is a practical publication safeguard, not a legal determination. Company approval and applicable confidentiality obligations take precedence.
