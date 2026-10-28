
# Bee's Boutique Invoice Automation

**Technologies:** JavaScript, Google Apps Script, Google Sheets, Square API

**Status:** Sandbox testing successful | Production rollout pending

## Overview

This project automates the weekly invoicing process for Bee's Boutique, a retail business that manages approximately 200 customer orders per week.

The system uses Google Sheets to organize customer purchases and Google Apps Script to communicate with the Square API. It validates purchase records, matches customers to their Square accounts, and creates draft invoices in batches.

The goal is to reduce the time spent preparing invoices manually while maintaining accurate records and preventing duplicate billing.

## How It Works

The automation follows a weekly invoicing workflow:

1. Customer purchase records are imported into Google Sheets.
2. Claims are reviewed and organized by customer bin number.
3. Each bin is matched to a Square customer ID.
4. A preflight check identifies customers ready for invoicing.
5. Approved orders are processed in batches of up to 25 customers.
6. The Square API creates orders and draft invoices.
7. Invoice IDs, statuses, and processing details are recorded in Google Sheets.
8. Completed weekly records can be archived before starting a new billing cycle.

Invoices remain in draft status for review. The automation does not automatically publish or send invoices to customers.

## Features

### Batch Invoice Processing

The system processes up to 25 customers per execution.

Each subsequent execution checks the invoice log and processes the next group of eligible customers.

This approach helps manage Google Apps Script execution limits while supporting larger weekly workloads.

For a typical week of 200 customers, the system is designed to process the workload across approximately eight batches.

### Duplicate Prevention

To reduce the risk of duplicate invoices, the automation:

- Checks the billing week and Square customer ID against existing invoice records.
- Skips customers who have already been logged.
- Records a reservation before making invoice-creation requests.
- Uses idempotency keys when communicating with Square.
- Preserves incomplete transactions for reconciliation before retrying.

### Preflight Validation

Before invoice creation, the system checks customer and purchase information.

Customers are categorized as:

- **Ready:** Eligible for draft invoice creation.
- **Already Logged:** Previously processed or reserved for the billing week.
- **Needs Review:** Requires attention before invoicing.

This allows issues to be identified before submitting requests to Square.

### Invoice Tracking

A dedicated Google Sheets invoice log records:

- Billing week
- Customer ID
- Bin number
- Square invoice ID
- Square order ID
- Invoice status
- Order total
- Creation timestamp
- Processing notes
- Idempotency keys

The log supports duplicate checking, troubleshooting, and tracking invoice activity.

### Error Handling

The automation includes safeguards for failed API requests and interrupted executions.

If an invoice request fails or its outcome is uncertain, the system preserves the reservation rather than automatically retrying.

Unresolved transactions must be reconciled before another attempt is made.

### Environment Controls

The project uses separate Square Sandbox and production configurations.

Additional safeguards include:

- Environment validation
- Authorized spreadsheet verification
- A configurable invoice-creation lock
- Script execution locking
- Draft-only invoice creation
- API credentials stored in Google Apps Script properties

Production invoice creation remains disabled until explicitly enabled.

## Technologies Used

| Technology | Purpose |
|---|---|
| JavaScript | Automation logic and data processing |
| Google Apps Script | Spreadsheet automation and API requests |
| Google Sheets | Customer data, purchase records, and invoice tracking |
| Square API | Customer management, order creation, and draft invoicing |
| REST API / JSON | Communication and data exchange with Square |
| GitHub | Version control and project documentation |

## Testing

Development and testing were performed using Square Sandbox to avoid affecting live customer accounts.

### Batch Processing Test

A test environment was configured with 30 simulated customers, each assigned a unique bin number and Square Sandbox customer ID.

Each customer was given a sample purchase to verify the complete invoicing workflow.

| Test | Result |
|---|---|
| First batch | 25 draft invoices created |
| Second batch | 5 draft invoices created |
| Total | 30 draft invoices created |
| Invoice logging | All 30 recorded as DRAFT |
| Final preflight | 0 customers ready |
| Duplicate recognition | All 30 test customers recognized as already processed |
| Customers needing review | 0 |

### Test Results

The 30-customer test successfully demonstrated that:

- Draft invoices can be created through the Square API.
- The system processes customers across multiple executions.
- Previously processed customers are excluded from subsequent batches.
- Invoice details are recorded in Google Sheets.
- The final preflight identifies no remaining eligible customers.
- No invoices were published or sent.

The final preflight reported:

- **Ready:** 0
- **Already Logged:** 36 (30 new test customers and 6 earlier test records)
- **Needs Review:** 0

The 25-customer batch limit was successfully tested. A full 200-customer production workload has not yet been completed.

## Project Structure

The Google Sheets workbook includes several components:

| Sheet | Purpose |
|---|---|
| Import Claims | Initial customer purchase imports |
| Live Claims | Active purchase records used for invoicing |
| Weekly Bins | Customer bin numbers and Square customer mappings |
| Square Invoice Log | Invoice statuses, identifiers, and processing history |

Google Apps Script handles validation, API communication, invoice creation, and weekly workflow management.

## Development Progress

### Completed

- [x] Set up the Google Sheets invoicing structure
- [x] Connect Google Apps Script to the Square API
- [x] Implement customer matching
- [x] Implement preflight validation
- [x] Build automated draft invoice creation
- [x] Add invoice logging
- [x] Add duplicate-prevention safeguards
- [x] Implement 25-customer batch processing
- [x] Create and configure Sandbox test customers
- [x] Successfully process 30 draft invoices across two batches
- [x] Verify invoice statuses and final preflight results
- [x] Configure production safety controls

### Remaining

- [ ] Validate the updated non-blocking batch notification
- [ ] Complete the first full production invoicing cycle
- [ ] Test performance with approximately 200 weekly customers
- [ ] Improve automated recovery for interrupted invoice requests
- [ ] Evaluate automatic execution of consecutive batches

## Current Status

**Sandbox testing successful. Production rollout pending.**

The system has successfully created 30 draft invoices across two batches using Square Sandbox.

Batch processing, invoice logging, and recognition of previously processed customers have been verified.

The production workflow is configured with safety controls and will be tested with real weekly customer orders before full deployment.


The long-term goal is a reliable invoicing workflow that can process approximately 200 customers per week with minimal manual intervention.
