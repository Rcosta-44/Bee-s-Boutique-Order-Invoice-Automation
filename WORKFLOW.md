# Workflow and operating notes

## Data structure

The claims import format used in the project includes:

| Column | Field |
|---|---|
| A | Week ID (plain text, `YYYY-MM-DD`) |
| B | Live Date |
| C | Sheet ID (unique row identifier) |
| D | Bin Number |
| E | Product Name |
| F | Unit Price |
| G | Quantity |
| H | Line Total |
| I | Review Status |
| J | Notes |
| K | Optional grand-total calculation (`=SUM(H2:H)`) |

**Normalization rule:** If bin 90 appears twice in a $25 sunglasses category and once as `90 x 2`, that is quantity 4 for that product. A claim for bin 90 under a different product remains a separate line.

## Draft creation

1. Import claims and review exceptions.
2. Confirm verified Square customer mappings.
3. Run invoice preflight.
4. Start weekly draft processing through the Sheets menu.
5. Controller persists state and schedules the first batch.
6. Each continuation creates up to 25 eligible drafts and updates the invoice log.
7. Controller schedules another batch if work remains, otherwise marks `COMPLETE`.
8. Review log and Square records before any customer email activity.

## State model

- `RUNNING`: More processing is scheduled or pending.
- `PROCESSING`: A batch has started; do not launch another run.
- `COMPLETE`: Controller believes all currently eligible drafts were processed.
- `STOPPED`: Manual investigation is required.

A completed controller state should be verified against the invoice log and Square; it is not a substitute for reconciliation.

## Sending workflow (developed, not fully production-tested)

1. Preview eligible draft invoices.
2. Verify recipient, total, tax, and due date.
3. Test publishing one explicitly approved invoice.
4. Confirm delivery and update the log.
5. Only after validation, consider controlled batch publishing with an explicit confirmation gate.

**Never run the publish function merely to test that the menu works. Publishing can send real customer emails.**

## Troubleshooting examples

- **Invalid Square payment method:** Check accepted payment method fields against the current API schema.
- **Due date earlier than scheduled date:** Reconcile Square invoice scheduling rules before retrying.
- **`ORDER_CREATED` without draft:** Inspect the existing Square order and log before attempting recovery.
- **Execution appears stuck:** Read batch state and logs; do not rerun automatically.
- **Invalid Week ID on import:** Confirm Column A is plain text and uses `YYYY-MM-DD`.
