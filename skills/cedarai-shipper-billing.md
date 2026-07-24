---
name: Export charges and reconcile shipper invoices
description: Pull the charges table for finance export and retrieve shipper invoices for reconciliation in Cedar ARMS.
api: openapi/cedarai-charge-openapi.yml
operations: [ListChargesTableDef, ListChargesTableSummary, ListChargesTableRows, ListInvoices, GetInvoice, AttachVoucher]
---

# Export charges and reconcile shipper invoices

Feed downstream finance systems (NetSuite, Sage, ERP middleware) from Cedar ARMS billing data.

## Auth
Charges use the ARMS gRPC-HTTP host (`api-lg-k-h1.arms.cedarai.com`); shipper invoices use `api-lg-k.arms.cedarai.com` (`.se` for EU). Send `x-arms-api-key` + `x-arms-assume-user` and `carrierId`. See `authentication/cedarai-authentication.yml`.

## Steps
1. `ListChargesTableDef` first — returns the available export columns and defaults so you can map Cedar fields (e.g. `chargeId`, `invoice`) to your target system.
2. `ListChargesTableSummary` to validate export scope and reconcile totals without pulling full rows.
3. `ListChargesTableRows` with your chosen columns, filters, sorting and pagination to export the rows.
4. For invoice reconciliation, `ListInvoices` then `GetInvoice`; use `AttachVoucher` to associate a voucher to a shipper's invoice.

## Rules
- `chargeId` is the stable identifier for reconciliation and idempotent (upsert) imports — key your downstream records on it.
- Cursor pagination (`pageSize`/`pageNextToken`) applies to row listings; see `conventions/cedarai-conventions.yml`.
