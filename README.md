# Bee's Boutique — Order & Invoice Automation

**Google Sheets + Google Apps Script + Square API**

A custom workflow automation built for a small retail business to transform manually recorded customer purchases into organized weekly orders and **Square draft invoices**. The system supports batch claim imports, customer/bin matching, preflight validation, invoice logging, and an archived weekly reset.

> **Status:** End-to-end workflow tested with real Square draft invoices for individual customers. High-volume runs (e.g., 100–200 customers) and automatic chunked processing are **not yet validated**.

## Business problem

Customer purchases were tracked in a notebook during live shopping events. Preparing individual invoices required repetitive data entry and manual organization. The goal was to reduce duplicate entry and make weekly invoicing more consistent while retaining a human review step before invoices are sent.

## Workflow

```text
Notebook / typed order list
          |
          v
Formatted, tab-separated order rows
          |
          v
Google Sheets: Import Claims
          |
          v
Validation + Live Claims
          |
          v
Weekly Bins (customer / Square ID mapping)
          |
          v
Preflight: ready / already logged / needs review
          |
          v
Explicitly enabled Square API draft creation
          |
          v
Square draft invoices + invoice log
          |
          v
Manual review before sending
```

## Key features

- **Batch import:** Takes structured order rows from an import sheet and moves validated claims into the active weekly claims workflow.
- **Customer mapping:** Associates a bin number with a customer and Square Customer ID.
- **Preflight validation:** Checks approval status, product names, valid prices and quantities, missing customer mappings, duplicate bin assignments, and customer IDs mapped to multiple bins.
- **Duplicate-prevention controls:** Checks the current week's invoice log before creating new drafts. This is **not** a complete lookup of manually created invoices in Square.
- **Square REST API integration:** Creates Square orders and draft invoices without automatically publishing or sending them.
- **Operational safeguards:** Uses a production enable/disable property, confirmation prompts, script locks, and an invoice log.
- **Weekly reset:** Archives five operational tabs before clearing current-week transactional data, while preserving customer bin assignments; blocks reset if imports remain pending.

## Technology

| Component | Purpose |
|---|---|
| Google Apps Script / JavaScript | Workflow logic, validation, and API requests |
| Google Sheets | Order intake, customer mappings, claim review, invoice log |
| Square API | Customer-linked orders and draft invoices |
| REST / JSON | Request and response handling |
| Script Properties | Environment configuration and credentials (not committed to GitHub) |

## Validation performed

- Imported a multi-line customer order into the live claims sheet.
- Ran preflight successfully with one customer ready, zero previously logged, and zero requiring review.
- Created and confirmed a real **draft** invoice in Square without sending it.
- Repeated the draft-creation workflow with another customer order.

## Current limitations and next steps

- Large-scale invoicing performance has not been established.
- A configurable per-run invoice cap, resume/reconciliation workflow, and failure-recovery testing are planned before attempting large production batches.
- Invoice-log checks do not guarantee detection of drafts created manually outside this automation.
- Tax treatment and draft contents should be reviewed in Square before sending.
- Structured order rows are currently prepared from typed order information; the system does **not** automatically parse Google Docs or handwritten notebook pages.

## Security and privacy

This project handles business transaction data. **Do not publish** live API tokens, customer names, customer IDs, spreadsheet IDs, production invoice logs, or unredacted order exports. Square credentials are stored in Apps Script Script Properties rather than hard-coded in the source. A public code release should be reviewed and sanitized separately.

## Portfolio context

Designed and tested to streamline a real small-business invoicing workflow, combining practical troubleshooting, data validation, API integration, and operational risk controls.
