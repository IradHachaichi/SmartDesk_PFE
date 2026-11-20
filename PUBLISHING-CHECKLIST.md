# SmartDesk Repository — Publishing Checklist

## Repository content
- [ ] Replace the README with the reviewed version.
- [ ] Add all files under `docs/`.
- [ ] Add the final anonymized images under `images/`.
- [ ] Verify that every image path referenced in README exists.
- [ ] Check spelling, links, headings and equations in GitHub's Markdown preview.

## Images
- [ ] `images/architecture.png`
- [ ] `images/chat.png`
- [ ] `images/incident.png`
- [ ] `images/simulation-cascade.png`
- [ ] `images/risk-exposure.png`
- [ ] `images/sla-compliance.png`
- [ ] `images/workload-forecast.png`
- [ ] `images/assignees.png`
- [ ] `images/embedded-in-app.png`

## Accuracy
- [ ] Confirm that the 92.2% AI accuracy metric uses the same definition as the report.
- [ ] Confirm that 212 passing tests is still the correct test count.
- [ ] Confirm that the dataset counts may be disclosed publicly.
- [ ] Confirm that the model evaluation results may be disclosed publicly.
- [ ] Check the exact English labels used for all five Power BI pages.
- [ ] Ensure that no actual portfolio VaR/TVaR values or client-level results are published without approval.

## Confidentiality
- [ ] Complete `docs/confidentiality-checklist.md`.
- [ ] Remove all real client and personal information from screenshots.
- [ ] Remove credentials, tenant identifiers, internal endpoints and report IDs.
- [ ] Check the Git history for sensitive files or secrets.
- [ ] Do not publish the original PFE report without explicit authorization.

## Final review
- [ ] Open the repository in a logged-out/private browser window.
- [ ] Test all documentation links.
- [ ] Check the README on desktop and mobile.
- [ ] Confirm that the repository description and topics are set.

Suggested repository description:

`AI-powered client information retrieval, actuarial SLA risk modelling, and Power BI decision-support reporting.`

Suggested topics:

`data-science`, `artificial-intelligence`, `llm`, `nl-to-sql`, `actuarial-science`, `risk-modelling`, `monte-carlo`, `value-at-risk`, `python`, `power-bi`, `data-engineering`, `postgresql`
