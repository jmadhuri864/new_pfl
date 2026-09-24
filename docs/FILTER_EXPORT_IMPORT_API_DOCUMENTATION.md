# Filter, Export & Import API Documentation

> **Generated file — do not edit by hand.** Run `npm run docs:filters` after changing a filter definition, an export definition or a document list endpoint. `src/test/service/filterApiDocs.spec.ts` fails when this file is out of date.

Get All (list) and Excel Export APIs for the 15 transaction modules, and the common filtering they share.

- **Base URL:** `http://localhost:4000` (no global route prefix; the port comes from `PORT`), as in [`excel-import-export-api.md`](excel-import-export-api.md)
- **Authentication:** `Authorization: Bearer <access_token>` (or the `access_token` cookie). Get a token from `POST /auth/login` — see [excel-import-export-api.md § 1](excel-import-export-api.md#1-get-an-access-token-first).
- **Filter implementation:** `src/global/filters/` · **Export implementation:** `src/excel/export/` and `<module>/excel/*.export.ts`

## Contents

1. [API summary](#1-api-summary)
2. [Filter summary](#2-filter-summary)
3. [Authentication and authorization](#3-authentication-and-authorization)
4. [Common filtering behaviour](#4-common-filtering-behaviour)
5. [Error responses](#5-error-responses)
6. [Export file format](#6-export-file-format)
7. [Import APIs](#7-import-apis)
8. [Modules](#8-modules)
   1. [RFPA](#81-rfpa)
   2. [Deal Slip](#82-deal-slip)
   3. [GRN](#83-grn)
   4. [Inward Register](#84-inward-register)
   5. [AQR](#85-aqr)
   6. [Dump Register](#86-dump-register)
   7. [Delivery Challan – Customer](#87-delivery-challan-customer)
   8. [Delivery Challan – Stock Transfer](#88-delivery-challan-stock-transfer)
   9. [Other Delivery Challan](#89-other-delivery-challan)
   10. [Final Invoice](#810-final-invoice)
   11. [Return by Customer](#811-return-by-customer)
   12. [Return to Vendor](#812-return-to-vendor)
   13. [Second Sale](#813-second-sale)
   14. [Vehicle Dispatch](#814-vehicle-dispatch)
   15. [Multi Cash Voucher](#815-multi-cash-voucher)
   16. [Labour Payment Voucher](#816-labour-payment-voucher)
   17. [Transport Payment Voucher](#817-transport-payment-voucher)
   18. [Packing Material Voucher](#818-packing-material-voucher)
   19. [All Vouchers](#819-all-vouchers)

---

## 1. API summary

| Module | Method | Endpoint | Purpose | Filters | Pagination |
|---|---|---|---|---|---|
| RFPA | GET | `/rfpa` | Get records (list) | Yes | Yes |
| RFPA | GET | `/rfpa/export/excel` | Export Excel | Yes | No |
| Deal Slip | GET | `/dealSlip` | Get records (list) | Yes | Yes |
| Deal Slip | GET | `/dealSlip/export/excel` | Export Excel | Yes | No |
| GRN | GET | `/grns` | Get records (list) | Yes | Yes |
| GRN | GET | `/grns/export/excel` | Export Excel | Yes | No |
| Inward Register | GET | `/inwardRegister` | Get records (list) | Yes | No (returns all rows) |
| Inward Register | GET | `/inwardRegister/export/excel` | Export Excel | Yes | No |
| AQR | GET | `/aqr` | Get records (list) | Yes | No (returns all rows) |
| AQR | GET | `/aqr/export/excel` | Export Excel | Yes | No |
| Dump Register | GET | `/dumpRegister` | Get records (list) | Yes | Yes |
| Dump Register | GET | `/dumpRegister/export/excel` | Export Excel | Yes | No |
| Delivery Challan – Customer | GET | `/customer-delivery-challan` | Get records (list) | Yes | Yes |
| Delivery Challan – Customer | GET | `/customer-delivery-challan/export/excel` | Export Excel | Yes | No |
| Delivery Challan – Stock Transfer | GET | `/tranfer-delivery-challan` | Get records (list) | Yes | Yes |
| Delivery Challan – Stock Transfer | GET | `/tranfer-delivery-challan/export/excel` | Export Excel | Yes | No |
| Other Delivery Challan | GET | `/other-delivery-challan` | Get records (list) | Yes | Yes |
| Other Delivery Challan | GET | `/other-delivery-challan/export/excel` | Export Excel | Yes | No |
| Final Invoice | GET | `/final-invoice` | Get records (list) | Yes | Yes |
| Final Invoice | GET | `/final-invoice/export/excel` | Export Excel | Yes | No |
| Return by Customer | GET | `/returns` | Get records (list) | Yes | Yes |
| Return by Customer | GET | `/returns/export/excel` | Export Excel | Yes | No |
| Return to Vendor | GET | `/return-to-vendor` | Get records (list) | Yes | Yes |
| Return to Vendor | GET | `/return-to-vendor/export/excel` | Export Excel | Yes | No |
| Second Sale | GET | `/secondSales` | Get records (list) | Yes | Yes |
| Second Sale | GET | `/secondSales/export/excel` | Export Excel | Yes | No |
| Vehicle Dispatch | GET | `/vehicleDispatches` | Get records (list) | Yes | No (returns all rows) |
| Vehicle Dispatch | GET | `/vehicleDispatches/export/excel` | Export Excel | Yes | No |
| Multi Cash Voucher | GET | `/multiCashVoucher` | Get records (list) | Yes | Yes |
| Multi Cash Voucher | GET | `/multiCashVoucher/export/excel` | Export Excel | Yes | No |
| Labour Payment Voucher | GET | `/lpvoucher` | Get records (list) | Yes | Yes |
| Labour Payment Voucher | GET | `/lpvoucher/export/excel` | Export Excel | Yes | No |
| Transport Payment Voucher | GET | `/tpvoucher` | Get records (list) | Yes | Yes |
| Transport Payment Voucher | GET | `/tpvoucher/export/excel` | Export Excel | Yes | No |
| Packing Material Voucher | GET | `/pmpvoucher` | Get records (list) | Yes | Yes |
| Packing Material Voucher | GET | `/pmpvoucher/export/excel` | Export Excel | Yes | No |
| All Vouchers | GET | `/vouchers` | Get records of all voucher types | Yes | Yes (when page and limit are given) |
| All Vouchers | GET | `/vouchers/export/excel` | Export Excel (all voucher types) | Yes | No |

No import, import-preview, import-validation, import-result or import-history API exists for these modules — see [§ 7](#7-import-apis).

---

## 2. Filter summary

| Module | Date field | Search | Status | Approval status | Customer | Vendor | Farmer | Location / warehouse | Product | Amount range | Other filters |
|---|---|---|---|---|---|---|---|---|---|---|---|
| RFPA | `rfpa.createdAt` | Yes | Yes | — | — | id, code, name | id, code, name | Yes | Yes | — | `source`, `requestingDepartment`, `isDealSlipCreated` |
| Deal Slip | `deal_slips.dealSlipCreatedAt` | Yes | Yes | Yes | — | id, code, name | id, code, name | Yes | Yes | — | `requestingDepartment`, `dealSlipNo`, `lotNo`, `loadingLocation`, `rfpaNo`, `isGrnCreated` |
| GRN | `grns.createdAt` | Yes | Yes | — | — | id, code, name | id, code, name | Yes | Yes | Yes | `grnType`, `purchaseType`, `locationType`, `source`, `paymentStatus`, `requestingDepartment`, `companyName`, `dealSlipNo`, `rfpaNo`, `vehicleNo`, `billNo` |
| Inward Register | `inward_register.date` | Yes | Yes | — | id, code, name | id, code, name | id, code, name | Yes | Yes | Yes | `inwardType`, `source`, `batchNo`, `grnNo`, `deliveryChallanNo`, `rbcNo` |
| AQR | `aqr.arrivalDate` | Yes | Yes | — | — | id, code, name | id, code, name | Yes | Yes | — | `aqrFor`, `source`, `deliveryChallanNo`, `supplierName`, `arrivalDate`, `minQuantity`, `maxQuantity` |
| Dump Register | `dump_register.date` | Yes | Yes | — | — | — | — | Yes | Yes | Yes | `dumpType`, `batchNo`, `grnNo`, `deliveryChallanNo`, `rbcNo`, `requestedById`, `minQuantity`, `maxQuantity` |
| Delivery Challan – Customer | `delivery_challan_purchase.createdAt` | Yes | Yes | Yes | id, code, name | — | — | Yes | Yes | Yes | `requestingDepartment`, `grnNo`, `vehicleNo`, `driverName`, `isReturned`, `poNumber`, `isInvoiceCreated`, `isReturnByCustomerCreated` |
| Delivery Challan – Stock Transfer | `delivery_challan_purchase.createdAt` | Yes | Yes | Yes | — | — | — | Yes | Yes | Yes | `requestingDepartment`, `grnNo`, `vehicleNo`, `driverName`, `isReturned`, `stockTransferType`, `sourceLocationId`, `sourceLocation`, `destinationLocationId`, `destinationLocation` |
| Other Delivery Challan | `delivery_challan_purchase.createdAt` | Yes | Yes | Yes | name (free text) | — | — | Yes | Yes | Yes | `requestingDepartment`, `grnNo`, `vehicleNo`, `driverName`, `isReturned`, `customerContactNo` |
| Final Invoice | `invoices.invoiceDate` | Yes | Yes | — | id, code, name | — | — | Yes | Yes | Yes | `paymentStatus`, `deliveryChallanNo`, `poNumber`, `vehicleNo`, `placeOfSupply` |
| Return by Customer | `return_by_customer.date` | Yes | Yes | — | id, code, name | — | — | Yes | Yes | — | `deliveryChallanNo` |
| Return to Vendor | `return_to_vendor.returnDate` | Yes | Yes | — | — | id, code, name | — | Yes | Yes | Yes | `grnNo`, `returnReason` |
| Second Sale | `second_sale_document.saleDate` | Yes | Yes | — | name (free text) | — | — | Yes | Yes | Yes | `customerContactNo`, `deliveryChallanNo`, `paymentMode`, `reasonForSale` |
| Vehicle Dispatch | `dispatch.date` | Yes | Yes | — | name (free text) | — | — | — | — | Yes | `vehicleType`, `vehicleNo`, `driverName`, `deliveryChallanNo`, `clientGRNNo` |
| Multi Cash Voucher | `multiple_cash_voucher.createdAt` | Yes | Yes | Yes | — | — | — | Yes | — | Yes | `requestingDepartment`, `paymentMode`, `grnNo`, `receiverName`, `payReceivedFrom`, `debitCreditTo`, `deliveryChallanNo` |
| Labour Payment Voucher | `labour_payment_voucher.createdAt` | Yes | Yes | Yes | — | — | — | Yes | — | Yes | `requestingDepartment`, `paymentMode`, `grnNo`, `receiverName`, `payReceivedFrom`, `debitCreditTo`, `products` |
| Transport Payment Voucher | `transport_payment_voucher.createdAt` | Yes | Yes | Yes | — | — | — | Yes | Yes | Yes | `requestingDepartment`, `paymentMode`, `grnNo`, `receiverName`, `payReceivedFrom`, `debitCreditTo`, `vehicleNo`, `driverName`, `dispatchLocation`, `destinationLocation` |
| Packing Material Voucher | `packing_material_payment.createdAt` | Yes | Yes | Yes | — | — | — | Yes | — | Yes | `requestingDepartment`, `paymentMode`, `grnNo`, `receiverName`, `payReceivedFrom`, `debitCreditTo`, `sellerName`, `purpose` |
| All Vouchers | `createdAt` | Yes | Yes | Yes | — | — | — | Yes | Transport vouchers only | Yes | `voucherType` + every voucher filter |

Every module also supports: `documentNo`, `createdById`, `createdBy`, `approvedById`, `approvedBy`, `approvalStartDate`, `approvalEndDate`, `sort` (see [§ 4](#4-common-filtering-behaviour)).

---

## 3. Authentication and authorization

### Authentication

Every endpoint in this document is registered on a controller with the `deserializeUser` and `requireUser` middleware:

- Send `Authorization: Bearer <access_token>`, or the `access_token` cookie set by `POST /auth/login`.
- Missing token → `401 {"status":"fail","message":"You are not logged in"}`.
- Invalid, expired or blacklisted token, or unknown user → `401 {"status":"fail","message":"You need to re-authenticate. Please log in."}`.

### Authorization

No role or permission middleware (`checkPermission`) is applied to these routes. Access is controlled by **row visibility**: each list returns only the documents the logged-in user may see, and every filter is applied **inside** that set in SQL — a filter can narrow what a user sees, never widen it. The Export uses exactly the same visibility.

| Modules | A document is visible to | Implemented in |
|---|---|---|
| RFPA, Deal Slip, Inward Register, AQR, Vehicle Dispatch | the document creator, or a user in the approval flow’s **level-1 approver** block (any status) | `DocSingalApproverService.getAllSingleApprovalDocumentsByUserId` |
| Dump Register, Delivery Challan – Customer, Delivery Challan – Stock Transfer, Other Delivery Challan, Return by Customer, Return to Vendor, Second Sale | the document creator, or a user in the **level-1 or level-2 approver** block (any status) | `DocDoubleApproverService.getAllDocumentByUserIdForDoubleApprover` |
| GRN, Multi Cash Voucher, Labour Payment Voucher, Transport Payment Voucher, Packing Material Voucher | the document creator (any status); a **verifier** when the status is hold, VERIFIED, approved, FINALIZING, COMPLETE or REJECT; an **approver (levels 1–6)** when VERIFIED, approved, FINALIZING, COMPLETE or REJECT; a **first finalizer** when approved, FINALIZING (not yet first-finalized), COMPLETE or REJECT; a **second finalizer** when FINALIZING (already first-finalized), COMPLETE or REJECT | `DocumentbService.getAllDocumentByUserId` |
| Final Invoice | the document creator, or a user in the **level-1 or level-2 approver** block (invoice not deleted) | `FinalInvoiceService.getAll` (own query) |

Soft-deleted documents (`documents.isDeleted` / `deletedAt`) are never listed or exported.

---

## 4. Common filtering behaviour

### 4.1 One filter implementation for Get All and Export

```text
Get All  ─┐
          ├─ applyDocumentListFilters()  →  parseDocumentFilters()  →  applyDocumentFilters() (SQL)
Export   ─┘
```

Both endpoints of a module build their options through `applyDocumentListFilters` (`src/global/filters/documentListOptions.ts`) with the same query string, so the same parameters always select the same records. The only difference:

|  | Get All | Export |
|---|---|---|
| Filters, search | Yes | Yes — identical |
| Sorting | Yes | Yes — identical order |
| Pagination (`page`, `limit`) | Yes (see each module) | **No** — every matching record is exported |
| Response | JSON | `.xlsx` file |

Order of operations, in the database: **visibility → filters → search → sort → pagination (Get All only)**.

### 4.2 Date filters

- `startDate` and `endDate` take a calendar day, `YYYY-MM-DD`. `dateFrom` / `dateTo` are accepted as aliases.
- Both bounds are **inclusive**. `endDate=2026-09-15` includes the whole day, up to 23:59:59.999.
- Days are India Standard Time (Asia/Kolkata), like every date the application shows.
- Either bound may be used alone; with neither, no date restriction applies.
- A malformed or impossible date (`15-09-2026`, `2026-02-30`) → `400`.
- `startDate` after `endDate` → `400`. The same rules apply to `approvalStartDate` / `approvalEndDate`.
- Each module filters on its business date (below). Modules without a document-date column use the record creation date.

| Module | startDate / endDate filter on |
|---|---|
| RFPA | `rfpa.createdAt` (RFPA has no document date column) |
| Deal Slip | `deal_slips.dealSlipCreatedAt` |
| GRN | `grns.createdAt` (GRN has no document date column) |
| Inward Register | `inward_register.date` |
| AQR | `aqr.arrivalDate` |
| Dump Register | `dump_register.date` |
| Delivery Challan – Customer | `delivery_challan_purchase.createdAt` (no challan date column) |
| Delivery Challan – Stock Transfer | `delivery_challan_purchase.createdAt` (no challan date column) |
| Other Delivery Challan | `delivery_challan_purchase.createdAt` (no challan date column) |
| Final Invoice | `invoices.invoiceDate` |
| Return by Customer | `return_by_customer.date` |
| Return to Vendor | `return_to_vendor.returnDate` |
| Second Sale | `second_sale_document.saleDate` |
| Vehicle Dispatch | `dispatch.date` |
| Multi Cash Voucher | `multiple_cash_voucher.createdAt` (no voucher date column) |
| Labour Payment Voucher | `labour_payment_voucher.createdAt` (no voucher date column) |
| Transport Payment Voucher | `transport_payment_voucher.createdAt` (no voucher date column) |
| Packing Material Voucher | `packing_material_payment.createdAt` (no voucher date column) |

Deal Slip uses `deal_slips.createdAt` when `dealSlipCreatedAt` is empty. Date columns are compared as calendar days; timestamp columns (`createdAt`, `dealSlipCreatedAt`) are compared against the IST day bounds.

### 4.3 Combining filters

All filters are combined with **AND**:

```text
startDate AND endDate AND status AND vendorId AND locationId AND productId AND ...
```

Inside a single filter, OR applies only where the parameter itself says so:

- a comma-separated list (`status=COMPLETE,hold`, `vendorId=<id1>,<id2>`) matches any of the values;
- `locationId` / `location` match any of the module’s location fields (e.g. GRN `location`, `purchaseLocation`, `purchaseForSalesLocation`);
- product filters match when **at least one** line item matches — the parent document is returned once, however many lines match.

### 4.4 Value formats

| Kind | Format | Matching |
|---|---|---|
| Date | `YYYY-MM-DD` | Inclusive whole day (IST) |
| ID | UUID; comma-separated for several | Exact, on the foreign key (a record still matches an id whose related row was later soft-deleted) |
| Enum | Allowed value; comma-separated for several | Exact (value case is normalised); unknown value → `400` listing the allowed values |
| Text | Any text | Partial, case-insensitive; `%` and `_` are matched literally |
| Case-insensitive list | Comma-separated | Exact, case-insensitive |
| Boolean | `true`/`false`, `1`/`0`, `yes`/`no` | Exact; other values → `400` |
| Number | Number | `minX` ≥, `maxX` ≤; non-number → `400`; `minX` > `maxX` → `400` |

- An absent or empty parameter (`?status=`) applies no filter.
- Parameters a module does not support are ignored, as unknown query parameters always have been.

### 4.5 Search

`search` is a partial, case-insensitive match, applied in SQL before pagination. It matches if **any** of these contain the text: the module’s document number, the document creator’s name, the document status, the module’s search fields (listed per module), and — for modules with products — any line’s product name or product code.

### 4.6 Sorting

`sort=<field>:ASC|DESC` is the existing convention; `sortBy` + `sortOrder` are accepted as an alternative.

- Fields listed as **sortable** for a module order the query in the database, so they decide which rows land on which page.
- Any other field is never interpolated into SQL; the query falls back to its default order (newest document first).
- A direction other than `ASC` / `DESC` → `400`.

### 4.7 Pagination

| Module | Get All pagination |
|---|---|
| RFPA | `page` defaults to 1 and `limit` to 10 in the controller; always paginated. |
| Deal Slip | `page` defaults to 1 and `limit` to 10 in the controller; always paginated. |
| GRN | When absent, the service uses page 1 and limit 10; always paginated. |
| Inward Register | Not applied (pre-existing behaviour): every filtered row is returned. `page` is echoed and `totalPages` is computed with `limit` (default 10). |
| AQR | Not applied (pre-existing behaviour): every filtered row is returned. `page` is echoed and `totalPages` is computed with `limit` (default 10). |
| Dump Register | When absent, page 1 and limit 10 are used (SQL `OFFSET`/`LIMIT`); always paginated. |
| Delivery Challan – Customer | When absent, page 1 and limit 10 are used (SQL `OFFSET`/`LIMIT`); always paginated. |
| Delivery Challan – Stock Transfer | When absent, page 1 and limit 10 are used (SQL `OFFSET`/`LIMIT`); always paginated. |
| Other Delivery Challan | When absent, page 1 and limit 10 are used (SQL `OFFSET`/`LIMIT`); always paginated. |
| Final Invoice | When absent, page 1 and limit 10 are used (SQL `OFFSET`/`LIMIT`); always paginated. |
| Return by Customer | When absent, page 1 and limit 10 are used (SQL `OFFSET`/`LIMIT`); always paginated. |
| Return to Vendor | `page` defaults to 1 and `limit` to 10 in the controller; always paginated. |
| Second Sale | When absent, page 1 and limit 10 are used (SQL `OFFSET`/`LIMIT`); always paginated. |
| Vehicle Dispatch | Not applied (pre-existing behaviour): every filtered row is returned. `page` is echoed and `totalPages` is computed with `limit` (default 10). |
| Multi Cash Voucher | Applied only when **both** `page` and `limit` are given; otherwise every filtered row is returned (`totalPages` = 1). |
| Labour Payment Voucher | Applied only when **both** `page` and `limit` are given; otherwise every filtered row is returned (`totalPages` = 1). |
| Transport Payment Voucher | Applied only when **both** `page` and `limit` are given; otherwise every filtered row is returned (`totalPages` = 1). |
| Packing Material Voucher | Applied only when **both** `page` and `limit` are given; otherwise every filtered row is returned (`totalPages` = 1). |

Export ignores `page` and `limit` in every module.

### 4.8 Parameters every module supports

| Parameter | Type | Description | Example |
|---|---|---|---|
| `startDate` | date `YYYY-MM-DD` | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo | `2026-09-15` |
| `status` | enum (comma-separated) | Document workflow status, comma separated (documents.status) | `COMPLETE,hold` |
| `search` | string | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | Alternative to `sort` | `createdAt`, `DESC` |
| `dateFrom`, `dateTo` | date | Aliases of `startDate`, `endDate` | `2026-09-01` |

`status` values (`documents.status`): `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED`.

`approvedById` / `approvedBy` / `approvalStartDate` / `approvalEndDate` match documents where a user **approved or verified** at any stage (verifier, approver levels 1–3, finalizer levels 1–2), using the recorded approval stages. All four combine on the same stage action.

### 4.9 Product parameters

Available on every module with line items (and on AQR, whose product is on the record):

| Parameter | Type | Description | Example |
|---|---|---|---|
| `productId` | uuid (comma-separated) | Product id(s) on any line | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | Product code on any line (partial) | `PRD001` |
| `productName` | string | Product name on any line (partial) | `Tomato` |
| `variantId` | uuid (comma-separated) | Variant id(s) on any line | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | Variant name on any line (partial) | `Hybrid` |
| `categoryId` | uuid (comma-separated) | Product category id(s) on any line | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | Product category name on any line (partial) | `Vegetables` |

---

## 5. Error responses

All errors use the application’s standard body, produced by the global error handler in `src/app.ts`:

```json
{ "status": "fail", "message": "..." }
```

`status` is `"fail"` for 4xx and `"error"` for 5xx.

| Status | When | Example message |
|---|---|---|
| `400` | Invalid date, date range, id, enum, boolean, number, range or sort order (Get All **and** Export) | `Invalid range: startDate (2026-09-15) is greater than endDate (2026-09-01)` |
| `400` | Invalid `voucherType` (All Vouchers) | `Invalid voucherType 'petty'. Allowed values: multi-cash-voucher, labour-payment-voucher, transport-payment-voucher, packing-material-voucher` |
| `401` | Missing token | `You are not logged in` |
| `401` | Invalid or expired token | `You need to re-authenticate. Please log in.` |
| `403` | Not returned by these endpoints (no role/permission checks; see § 3) | — |
| `404` | Get All with no matching records — **Customer DC and Final Invoice only** (other lists return `200` with an empty `data`) | `No customer delivery challans found` |
| `404` | Export with no matching records (every module) | `No records found for the given filters` |
| `500` | Unexpected server error | `Internal Server Error` |

Validation messages by filter kind: `Invalid <param> '<value>'. Expected a date in YYYY-MM-DD format` · `Expected a UUID` · `Expected a number` · `Expected true or false` · `Allowed values: ...` · `Invalid sort order '<value>'. Expected ASC or DESC`.

If an export fails **after** the file has started streaming, the headers are already sent, so no JSON error is possible: the connection is closed and the download fails rather than delivering a truncated file.

---

## 6. Export file format

| Response header | Value |
|---|---|
| `Content-Type` | `application/vnd.openxmlformats-officedocument.spreadsheetml.sheet` |
| `Content-Disposition` | `attachment; filename="<Stem>_<YYYY-MM-DD>.xlsx"; filename*=UTF-8''<Stem>_<YYYY-MM-DD>.xlsx` |
| `Cache-Control` | `no-store` |
| `X-Export-Record-Count` | Number of documents in the file |
| `Access-Control-Expose-Headers` | `Content-Disposition, X-Export-Record-Count` |

- The file name date is today in IST, e.g. `GRN_2026-09-15.xlsx`.
- Format: `.xlsx`, streamed in chunks of 500 documents, so large exports use bounded memory and no `Content-Length` is sent.
- Every sheet has a bold, frozen header row and an auto-filter.
- Documents appear in the Get All order, and line items follow their document.
- A sheet that would exceed Excel’s 1,048,575 data rows continues on `<Sheet> (2)`.
- Every document export ends with an **Approvals** sheet: one row per approval action (stage, action, user, reason, date).
- Every document sheet includes the approval summary columns: Document ID, Overall Status, last approval stage/action/user/date, approved by/date, rejected by/date/reason, document creator and timestamps.

Cell formats:

| Column type | Excel format |
|---|---|
| `text` | Text |
| `integer` | Number `0` |
| `number` | Number `#,##0.####` |
| `amount` | Number `#,##0.00` |
| `quantity` | Number `#,##0.###` |
| `percent` | Number `0.00"%"` (12.5 means 12.5%) |
| `date` | Date `dd-mm-yyyy` |
| `datetime` | Date-time in IST `dd-mm-yyyy hh:mm AM/PM` |
| `time` | Text (`HH:mm[:ss]`) |
| `boolean` | Text `Yes` / `No` |

---

## 7. Import APIs

None of the 15 modules in this document has an import API. There are no import-preview, import-validation, import-result or import-history endpoints for them in the backend.

- The only Excel import endpoints in the project are for master data: products, farmers, customers and vendors. They are documented in [`excel-import-export-api.md`](excel-import-export-api.md).
- The file uploads on these modules (`uploadAttachments` on the delivery challans, `billImage` on GRN) attach files while creating or updating one document; they are not imports.
- Because filters only make sense for listing, no filter parameters were added to any upload endpoint. The filter definitions and parser are reusable (`parseDocumentFilters`, `applyDocumentListFilters`) if an import preview, validation or result listing is added later.

---

## 8. Modules

### 8.1 RFPA

Document type `rfpa` · Record table: `rfpa` · Document number: `rfpaId`

#### Get All RFPA

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/rfpa` |
| **Purpose** | Lists the RFPA documents visible to the user, filtered, searched, sorted and paginated. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | No role check. Visible to the document creator, or a user in the approval flow’s **level-1 approver** block (any status). |
| **Implemented in** | `RfpaController.getAllRfpa → RfpaService.getAllRfpa` |
| **Path parameters** | None |

**Query parameters** (all optional)

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page` | number | No | Page number. `page` defaults to 1 and `limit` to 10 in the controller; always paginated. | `1` |
| `limit` | number | No | Records per page (see `page`). | `20` |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `rfpa.createdAt` (RFPA has no document date column) | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `rfpa.createdAt` (RFPA has no document date column) | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `source` | enum (comma-separated) | No | Source: vendor or farmer. Exact match; any of: `vendor`, `farmer` | `vendor` |
| `requestingDepartment` | enum (comma-separated) | No | Requesting department. Exact match; any of: `admin`, `hr`, `operations`, `sale`, `procurement`, `it`, `business_development`, `exports`, `branding_&_marketing`, `farming`, `quality_checking`, `other` | `admin` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `vendorId` | uuid (comma-separated) | No | Vendor id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `vendorCode` | string | No | Vendor code. Partial, case-insensitive | `VEN001` |
| `vendorName` | string | No | Vendor name. Partial, case-insensitive | `Agro Traders` |
| `farmerId` | uuid (comma-separated) | No | Farmer id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `farmerCode` | string | No | Farmer code. Partial, case-insensitive | `FAR001` |
| `farmerName` | string | No | Farmer name. Partial, case-insensitive | `Suresh Patil` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: purchaseLocation, purchaseForSalesLocation. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `isDealSlipCreated` | boolean | No | A deal slip has been raised. `true` / `false` (also `1`/`0`, `yes`/`no`) | `true` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |

**Search fields** (`search`): document number (`rfpaId`), creator name, document status, company name, vendor name, vendor code, farmer name, purchase location name, `deliveryReceivingPerson`, product name / code.

**Sortable in the database** (`sort`): `createdAt`, `createdDate`, `updatedAt`, `status`, `overAllStatus`, `documentNo`, `rfpaId`.

**Example request**

```http
GET /rfpa?startDate=2026-09-01&endDate=2026-09-15&status=COMPLETE&vendorId=9b2d4c1e-5f6a-4b7c-8d9e-0a1b2c3d4e5f&productName=Tomato&page=1&limit=20
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/rfpa?startDate=2026-09-01&endDate=2026-09-15&status=COMPLETE&vendorId=9b2d4c1e-5f6a-4b7c-8d9e-0a1b2c3d4e5f&productName=Tomato&page=1&limit=20" \
  -H "Authorization: Bearer <token>"
```

**Response `200`**

| Field | Type | Description |
|---|---|---|
| `status` | string | `"success"` |
| `data` | object[] | The records (fields below) |
| `allRecords` | number | Total matching records |
| `totalPages` | number | Total pages |
| `page` | number | Current page |

```json
{
  "status": "success",
  "data": [
    {
      "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "documentId": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "overAllStatus": "hold",
      "createdBy": "string",
      "createdDate": "2026-09-15",
      "createdTime": "10:30 AM",
      "rfpaId": "string",
      "source": "string",
      "companyName": "string",
      "purchaseLocation": "string",
      "purchaseForSalesLocation": "string",
      "deliveryReceivingPerson": "string",
      "packingInstruction": "string",
      "remark": "string",
      "paymentInfo": {
        "paymentMode": "string",
        "paymentDate": "string",
        "advancePaidAmt": "0.00",
        "paymentTerms": "string",
        "dueDate": "string",
        "creditPeriod": 0,
        "validityOfQuote": "string"
      }
    }
  ],
  "allRecords": 1,
  "totalPages": 1,
  "page": 1
}
```

`data[]` fields:

| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Record id of the module document |
| `documentId` | string (uuid) | Approval document id (`documents.id`), used by the `/view/:docid` endpoints |
| `overAllStatus` | string | Document workflow status (`documents.status`) |
| `createdBy` | string | Document creator's name |
| `createdDate` | string | Creation date, `YYYY-MM-DD` |
| `createdTime` | string | Creation time, `hh:mm A` |
| `rfpaId` | string | RFPA number |
| `source` | string | vendor or farmer |
| `companyName` | string \| null | Company name |
| `purchaseLocation` | string \| null | Purchase location name |
| `purchaseForSalesLocation` | string \| null | Purchase-for-sales location name |
| `deliveryReceivingPerson` | string \| null | Delivery receiving person |
| `packingInstruction` | string \| null | Packing instruction |
| `remark` | string \| null | Remark |
| `paymentInfo` | object \| null | Payment terms (see below) |

`paymentInfo` fields:

| Field | Type | Description |
|---|---|---|
| `paymentMode` | string \| null | Payment mode |
| `paymentDate` | string \| null | Payment date |
| `advancePaidAmt` | string \| null | Advance paid (decimal, serialised as a string) |
| `paymentTerms` | string \| null | Payment terms |
| `dueDate` | string \| null | Due date |
| `creditPeriod` | number \| null | Credit period |
| `validityOfQuote` | string \| null | Validity of quote |

**No matching records:** `200` with `data: []` and a total of `0`.

**Errors:** `400` invalid filter · `401` not authenticated · `500` unexpected error (see [§ 5](#5-error-responses)).

#### Export RFPA

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/rfpa/export/excel` |
| **Purpose** | Downloads every RFPA document matching the filters as an `.xlsx` workbook. Same filters, search, sort and visibility as Get All; **no pagination**. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | Same row visibility as Get All. |
| **Path parameters** | None |

**Query parameters:** every Get All parameter above; `page` and `limit` are ignored.

<details><summary>Full parameter list</summary>

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page`, `limit` | number | No | **Ignored** — the export always contains every matching record. | — |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `rfpa.createdAt` (RFPA has no document date column) | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `rfpa.createdAt` (RFPA has no document date column) | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `source` | enum (comma-separated) | No | Source: vendor or farmer. Exact match; any of: `vendor`, `farmer` | `vendor` |
| `requestingDepartment` | enum (comma-separated) | No | Requesting department. Exact match; any of: `admin`, `hr`, `operations`, `sale`, `procurement`, `it`, `business_development`, `exports`, `branding_&_marketing`, `farming`, `quality_checking`, `other` | `admin` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `vendorId` | uuid (comma-separated) | No | Vendor id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `vendorCode` | string | No | Vendor code. Partial, case-insensitive | `VEN001` |
| `vendorName` | string | No | Vendor name. Partial, case-insensitive | `Agro Traders` |
| `farmerId` | uuid (comma-separated) | No | Farmer id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `farmerCode` | string | No | Farmer code. Partial, case-insensitive | `FAR001` |
| `farmerName` | string | No | Farmer name. Partial, case-insensitive | `Suresh Patil` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: purchaseLocation, purchaseForSalesLocation. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `isDealSlipCreated` | boolean | No | A deal slip has been raised. `true` / `false` (also `1`/`0`, `yes`/`no`) | `true` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |

</details>

**Example request**

```http
GET /rfpa/export/excel?startDate=2026-09-01&endDate=2026-09-15&status=COMPLETE&vendorId=9b2d4c1e-5f6a-4b7c-8d9e-0a1b2c3d4e5f&productName=Tomato
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/rfpa/export/excel?startDate=2026-09-01&endDate=2026-09-15&status=COMPLETE&vendorId=9b2d4c1e-5f6a-4b7c-8d9e-0a1b2c3d4e5f&productName=Tomato" \
  -H "Authorization: Bearer <token>" \
  --output RFPA.xlsx
```

**Response `200`**

```text
Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Content-Disposition: attachment; filename="RFPA_2026-09-15.xlsx"; filename*=UTF-8''RFPA_2026-09-15.xlsx
Cache-Control: no-store
X-Export-Record-Count: <documents in the file>
```

File name pattern: `RFPA_<YYYY-MM-DD>.xlsx`. Sheets:

| Sheet | Columns | Column headers (in order) |
|---|---|---|
| `RFPA` | 53 | RFPA No, RFPA Record ID, Requesting Department, Source, Company, Company GST No, Purchase Location, Other Purchase Location, Purchase For Sales Location, Other Purchase For Sales Location, Vendor ID, Vendor Name, Vendor Code, Vendor GSTN, Vendor Contact No, Farmer ID, Farmer Name, Farmer Code, Farmer Mobile No, Delivery Receiving Person, Packing Instruction, Special Requirement, Remark, Deal Slip Created, Line Items, Items Total Amount, Payment Mode, Payment Date, Advance Paid Amount, Payment Terms, Due Date, Credit Period, Validity Of Quote, Created By, Created By Employee ID, Created Date, Updated Date, Document ID, Overall Status, Last Approval Stage, Last Approval Action, Last Action By, Last Action Date, Approved By, Approval Date, Rejected By, Rejection Date, Rejection Reason, Document Remarks, Document Created By, Document Created By Employee ID, Document Created Date, Document Updated Date |
| `RFPA Items` | 24 | RFPA No, RFPA Record ID, Item ID, Product ID, Product Code, Product Name, Category, Variant, Variant Code, Grade, Count, Size, Origin, Variety, Quantity, UOM, Unit Price, Amount, Purchase Date, Expected Harvest Date, Dispatch Date, Delivery Date, Created Date, Updated Date |
| `Approvals` | 11 | RFPA No, Record ID, Document ID, Overall Status, Stage, Stage Order, Action, Action By, Action By User ID, Reason, Action Date |

**Errors:** `400` invalid filter (JSON) · `401` not authenticated · `404 {"status":"fail","message":"No records found for the given filters"}` · `500` unexpected error.

#### Import RFPA

Not available — no import endpoint exists for this module (see [§ 7](#7-import-apis)).

---

### 8.2 Deal Slip

Document type `deal-slip` · Record table: `deal_slips` · Document number: `dealSlipNo`

#### Get All Deal Slip

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/dealSlip` |
| **Purpose** | Lists the Deal Slip documents visible to the user, filtered, searched, sorted and paginated. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | No role check. Visible to the document creator, or a user in the approval flow’s **level-1 approver** block (any status). |
| **Implemented in** | `DealSlipController.getAllDealSlips → DealSlipService.getAllDealSlips` |
| **Path parameters** | None |

**Query parameters** (all optional)

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page` | number | No | Page number. `page` defaults to 1 and `limit` to 10 in the controller; always paginated. | `1` |
| `limit` | number | No | Records per page (see `page`). | `20` |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `deal_slips.dealSlipCreatedAt` | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `deal_slips.dealSlipCreatedAt` | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `approvalStatus` | enum (comma-separated) | No | Deal slip approval status. Exact match; any of: `pending`, `approved`, `notapproved`, `active`, `incomplete`, `draft` | `pending` |
| `requestingDepartment` | enum (comma-separated) | No | Requesting department. Exact match; any of: `admin`, `hr`, `operations`, `sale`, `procurement`, `it`, `business_development`, `exports`, `branding_&_marketing`, `farming`, `quality_checking`, `other` | `admin` |
| `dealSlipNo` | string | No | Deal slip number. Partial, case-insensitive | `DS2026` |
| `lotNo` | string | No | Lot number. Partial, case-insensitive | `abc` |
| `loadingLocation` | string | No | Loading location. Partial, case-insensitive | `abc` |
| `rfpaNo` | string | No | RFPA number. Partial, case-insensitive | `RFPA2026` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `vendorId` | uuid (comma-separated) | No | Vendor id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `vendorCode` | string | No | Vendor code. Partial, case-insensitive | `VEN001` |
| `vendorName` | string | No | Vendor name. Partial, case-insensitive | `Agro Traders` |
| `farmerId` | uuid (comma-separated) | No | Farmer id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `farmerCode` | string | No | Farmer code. Partial, case-insensitive | `FAR001` |
| `farmerName` | string | No | Farmer name. Partial, case-insensitive | `Suresh Patil` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: purchaseLocation, purchaseForSalesLocation. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `isGrnCreated` | boolean | No | A GRN has been raised. `true` / `false` (also `1`/`0`, `yes`/`no`) | `true` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |

**Search fields** (`search`): document number (`dealSlipNo`), creator name, document status, `lotNo`, `loadingLocation`, RFPA number, company name, vendor name, vendor code, farmer name, product name / code.

**Sortable in the database** (`sort`): `createdAt`, `createdDate`, `updatedAt`, `status`, `overAllStatus`, `documentNo`, `dealSlipNo`, `dealSlipCreatedAt`.

**Example request**

```http
GET /dealSlip?startDate=2026-09-01&endDate=2026-09-15&approvalStatus=approved&rfpaNo=RFPA2026&vendorId=9b2d4c1e-5f6a-4b7c-8d9e-0a1b2c3d4e5f&page=1&limit=20
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/dealSlip?startDate=2026-09-01&endDate=2026-09-15&approvalStatus=approved&rfpaNo=RFPA2026&vendorId=9b2d4c1e-5f6a-4b7c-8d9e-0a1b2c3d4e5f&page=1&limit=20" \
  -H "Authorization: Bearer <token>"
```

**Response `200`**

| Field | Type | Description |
|---|---|---|
| `status` | string | `"success"` |
| `data` | object[] | The records (fields below) |
| `allRecords` | number | Total matching records |
| `totalPages` | number | Total pages |
| `page` | number | Current page |

```json
{
  "status": "success",
  "data": [
    {
      "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "documentId": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "overAllStatus": "hold",
      "createdBy": "string",
      "createdDate": "2026-09-15",
      "createdTime": "10:30 AM",
      "dealSlipNo": "string",
      "rfpa": "string",
      "lotNo": "string",
      "loadingLocation": "string",
      "specialRequest": "string",
      "remark": "string"
    }
  ],
  "allRecords": 1,
  "totalPages": 1,
  "page": 1
}
```

`data[]` fields:

| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Record id of the module document |
| `documentId` | string (uuid) | Approval document id (`documents.id`), used by the `/view/:docid` endpoints |
| `overAllStatus` | string | Document workflow status (`documents.status`) |
| `createdBy` | string | Document creator's name |
| `createdDate` | string | Creation date, `YYYY-MM-DD` |
| `createdTime` | string | Creation time, `hh:mm A` |
| `dealSlipNo` | string | Deal slip number |
| `rfpa` | string \| null | Linked RFPA number |
| `lotNo` | string \| null | Lot number |
| `loadingLocation` | string \| null | Loading location |
| `specialRequest` | string \| null | Special request |
| `remark` | string \| null | Remark |

**No matching records:** `200` with `data: []` and a total of `0`.

**Errors:** `400` invalid filter · `401` not authenticated · `500` unexpected error (see [§ 5](#5-error-responses)).

#### Export Deal Slip

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/dealSlip/export/excel` |
| **Purpose** | Downloads every Deal Slip document matching the filters as an `.xlsx` workbook. Same filters, search, sort and visibility as Get All; **no pagination**. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | Same row visibility as Get All. |
| **Path parameters** | None |

**Query parameters:** every Get All parameter above; `page` and `limit` are ignored.

<details><summary>Full parameter list</summary>

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page`, `limit` | number | No | **Ignored** — the export always contains every matching record. | — |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `deal_slips.dealSlipCreatedAt` | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `deal_slips.dealSlipCreatedAt` | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `approvalStatus` | enum (comma-separated) | No | Deal slip approval status. Exact match; any of: `pending`, `approved`, `notapproved`, `active`, `incomplete`, `draft` | `pending` |
| `requestingDepartment` | enum (comma-separated) | No | Requesting department. Exact match; any of: `admin`, `hr`, `operations`, `sale`, `procurement`, `it`, `business_development`, `exports`, `branding_&_marketing`, `farming`, `quality_checking`, `other` | `admin` |
| `dealSlipNo` | string | No | Deal slip number. Partial, case-insensitive | `DS2026` |
| `lotNo` | string | No | Lot number. Partial, case-insensitive | `abc` |
| `loadingLocation` | string | No | Loading location. Partial, case-insensitive | `abc` |
| `rfpaNo` | string | No | RFPA number. Partial, case-insensitive | `RFPA2026` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `vendorId` | uuid (comma-separated) | No | Vendor id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `vendorCode` | string | No | Vendor code. Partial, case-insensitive | `VEN001` |
| `vendorName` | string | No | Vendor name. Partial, case-insensitive | `Agro Traders` |
| `farmerId` | uuid (comma-separated) | No | Farmer id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `farmerCode` | string | No | Farmer code. Partial, case-insensitive | `FAR001` |
| `farmerName` | string | No | Farmer name. Partial, case-insensitive | `Suresh Patil` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: purchaseLocation, purchaseForSalesLocation. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `isGrnCreated` | boolean | No | A GRN has been raised. `true` / `false` (also `1`/`0`, `yes`/`no`) | `true` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |

</details>

**Example request**

```http
GET /dealSlip/export/excel?startDate=2026-09-01&endDate=2026-09-15&approvalStatus=approved&rfpaNo=RFPA2026&vendorId=9b2d4c1e-5f6a-4b7c-8d9e-0a1b2c3d4e5f
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/dealSlip/export/excel?startDate=2026-09-01&endDate=2026-09-15&approvalStatus=approved&rfpaNo=RFPA2026&vendorId=9b2d4c1e-5f6a-4b7c-8d9e-0a1b2c3d4e5f" \
  -H "Authorization: Bearer <token>" \
  --output Deal_Slip.xlsx
```

**Response `200`**

```text
Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Content-Disposition: attachment; filename="Deal_Slip_2026-09-15.xlsx"; filename*=UTF-8''Deal_Slip_2026-09-15.xlsx
Cache-Control: no-store
X-Export-Record-Count: <documents in the file>
```

File name pattern: `Deal_Slip_<YYYY-MM-DD>.xlsx`. Sheets:

| Sheet | Columns | Column headers (in order) |
|---|---|---|
| `Deal Slip` | 61 | Deal Slip No, Deal Slip Record ID, Lot No, RFPA No, RFPA Record ID, Requesting Department, Deal Slip Approval Status, Approval Note, Loading Location, Special Request, Remark, Deal Slip Created At, Deal Slip Approved At, GRN Created, Source, Company, Company GST No, Vendor ID, Vendor Name, Vendor Code, Vendor GSTN, Vendor Contact No, Farmer ID, Farmer Name, Farmer Code, Farmer Mobile No, Purchase Location, Other Purchase Location, Purchase For Sales Location, Other Purchase For Sales Location, Delivery Receiving Person, Packing Instruction, Line Items, Items Total Amount, Payment Mode, Payment Date, Advance Paid Amount, Payment Terms, Due Date, Credit Period, Validity Of Quote, Created By, Created By Employee ID, Created Date, Updated Date, Document ID, Overall Status, Last Approval Stage, Last Approval Action, Last Action By, Last Action Date, Approved By, Approval Date, Rejected By, Rejection Date, Rejection Reason, Document Remarks, Document Created By, Document Created By Employee ID, Document Created Date, Document Updated Date |
| `Deal Slip Items` | 23 | Deal Slip No, Deal Slip Record ID, RFPA No, Item ID, Product ID, Product Code, Product Name, Category, Variant, Variant Code, Grade, Count, Size, Origin, Variety, Quantity, UOM, Unit Price, Amount, Purchase Date, Expected Harvest Date, Dispatch Date, Delivery Date |
| `Approvals` | 11 | Deal Slip No, Record ID, Document ID, Overall Status, Stage, Stage Order, Action, Action By, Action By User ID, Reason, Action Date |

**Errors:** `400` invalid filter (JSON) · `401` not authenticated · `404 {"status":"fail","message":"No records found for the given filters"}` · `500` unexpected error.

#### Import Deal Slip

Not available — no import endpoint exists for this module (see [§ 7](#7-import-apis)).

---

### 8.3 GRN

Document type `grn` · Record table: `grns` · Document number: `grnNo`

#### Get All GRN

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/grns` |
| **Purpose** | Lists the GRN documents visible to the user, filtered, searched, sorted and paginated. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | No role check. Visible to the document creator (any status); a **verifier** when the status is hold, VERIFIED, approved, FINALIZING, COMPLETE or REJECT; an **approver (levels 1–6)** when VERIFIED, approved, FINALIZING, COMPLETE or REJECT; a **first finalizer** when approved, FINALIZING (not yet first-finalized), COMPLETE or REJECT; a **second finalizer** when FINALIZING (already first-finalized), COMPLETE or REJECT. |
| **Implemented in** | `GrnController.getAllGrns → GrnService.getAllGrns` |
| **Path parameters** | None |

**Query parameters** (all optional)

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page` | number | No | Page number. When absent, the service uses page 1 and limit 10; always paginated. | `1` |
| `limit` | number | No | Records per page (see `page`). | `20` |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `grns.createdAt` (GRN has no document date column) | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `grns.createdAt` (GRN has no document date column) | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `grnType` | enum (comma-separated) | No | GRN type. Exact match; any of: `transfer`, `purchase` | `transfer` |
| `purchaseType` | enum (comma-separated) | No | Purchase type. Exact match; any of: `fixed price sales`, `consignment sales / bikri`, `mgp sales` | `fixed price sales` |
| `locationType` | enum (comma-separated) | No | Location type. Exact match; any of: `cc`, `dc` | `cc` |
| `source` | enum (comma-separated) | No | Source: vendor or farmer. Exact match; any of: `vendor`, `farmer` | `vendor` |
| `paymentStatus` | enum (comma-separated) | No | Payment status. Exact match; any of: `paid`, `unpaid` | `paid` |
| `requestingDepartment` | enum (comma-separated) | No | Requesting department. Exact match; any of: `admin`, `hr`, `operations`, `sale`, `procurement`, `it`, `business_development`, `exports`, `branding_&_marketing`, `farming`, `quality_checking`, `other` | `admin` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `companyName` | uuid or string | No | Company id or name. UUID: exact id; otherwise partial, case-insensitive name | `Prime Fresh` |
| `dealSlipNo` | string | No | Deal slip number. Partial, case-insensitive | `DS2026` |
| `rfpaNo` | string | No | RFPA number. Partial, case-insensitive | `RFPA2026` |
| `vendorId` | uuid (comma-separated) | No | Vendor id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `vendorCode` | string | No | Vendor code. Partial, case-insensitive | `VEN001` |
| `vendorName` | string | No | Vendor name. Partial, case-insensitive | `Agro Traders` |
| `farmerId` | uuid (comma-separated) | No | Farmer id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `farmerCode` | string | No | Farmer code. Partial, case-insensitive | `FAR001` |
| `farmerName` | string | No | Farmer name. Partial, case-insensitive | `Suresh Patil` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: location, purchaseLocation, purchaseForSalesLocation. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `vehicleNo` | string | No | Vehicle number. Partial, case-insensitive | `MH12AB1234` |
| `billNo` | string | No | Bill number. Partial, case-insensitive | `B-101` |
| `minAmount` | number | No | GRN total amount >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | GRN total amount <= value. Inclusive upper bound | `50000` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |

**Search fields** (`search`): document number (`grnNo`), creator name, document status, `billNo`, `vehicleNo`, company name, deal slip number, RFPA number, vendor name, vendor code, farmer name, location name, product name / code.

**Sortable in the database** (`sort`): `createdAt`, `createdDate`, `updatedAt`, `status`, `overAllStatus`, `documentNo`, `grnNo`, `totalAmt`.

**Example request**

```http
GET /grns?startDate=2026-09-01&endDate=2026-09-15&status=COMPLETE&vendorId=9b2d4c1e-5f6a-4b7c-8d9e-0a1b2c3d4e5f&warehouseId=4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c&productId=1f2e3d4c-5b6a-4978-8a9b-0c1d2e3f4a5b&page=1&limit=20
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/grns?startDate=2026-09-01&endDate=2026-09-15&status=COMPLETE&vendorId=9b2d4c1e-5f6a-4b7c-8d9e-0a1b2c3d4e5f&warehouseId=4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c&productId=1f2e3d4c-5b6a-4978-8a9b-0c1d2e3f4a5b&page=1&limit=20" \
  -H "Authorization: Bearer <token>"
```

**Response `200`**

| Field | Type | Description |
|---|---|---|
| `status` | string | `"success"` |
| `data` | object[] | The records (fields below) |
| `allRecords` | number | Total matching records |
| `totalPages` | number | Total pages |
| `page` | number | Current page |

```json
{
  "status": "success",
  "data": [
    {
      "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "documentId": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "overAllStatus": "hold",
      "createdBy": "string",
      "createdDate": "2026-09-15",
      "createdTime": "10:30 AM",
      "grnNo": "string",
      "grnType": "string",
      "purchaseType": "string",
      "locationType": "string",
      "source": "string",
      "companyName": "string",
      "purchaseLocation": "string",
      "purchaseForSalesLocation": "string",
      "billNo": "string",
      "subTotalAmt": "0.00",
      "freight": "0.00",
      "otherCharges": "0.00",
      "totalAmt": "0.00",
      "amtWords": "string",
      "cratesIn": 0,
      "purchasedBy": "string",
      "receivedThrough": "string",
      "vehicleNo": "string",
      "timeIn": "string",
      "securityPerson": "string",
      "deliveryReceivingPerson": "string",
      "rmn": "string",
      "remark": "string",
      "paymentInfo": {
        "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
        "paymentMode": "string",
        "paymentDate": "string",
        "advancePaidAmt": "0.00",
        "paymentTerms": "string",
        "dueDate": "string",
        "creditPeriod": 0
      }
    }
  ],
  "allRecords": 1,
  "totalPages": 1,
  "page": 1
}
```

`data[]` fields:

| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Record id of the module document |
| `documentId` | string (uuid) | Approval document id (`documents.id`), used by the `/view/:docid` endpoints |
| `overAllStatus` | string | Document workflow status (`documents.status`) |
| `createdBy` | string | Document creator's name |
| `createdDate` | string | Creation date, `YYYY-MM-DD` |
| `createdTime` | string | Creation time, `hh:mm A` |
| `grnNo` | string | GRN number |
| `grnType` | string | transfer or purchase |
| `purchaseType` | string \| null | Purchase type |
| `locationType` | string \| null | cc or dc |
| `source` | string \| null | vendor or farmer |
| `companyName` | string \| null | Company name |
| `purchaseLocation` | string \| null | Purchase location name |
| `purchaseForSalesLocation` | string \| null | Purchase-for-sales location name |
| `billNo` | string \| null | Bill number |
| `subTotalAmt` | string \| null | Sub total (decimal, serialised as a string) |
| `freight` | string \| null | Freight (decimal, serialised as a string) |
| `otherCharges` | string \| null | Other charges (decimal, serialised as a string) |
| `totalAmt` | string \| null | Total amount (decimal, serialised as a string) |
| `amtWords` | string \| null | Amount in words |
| `cratesIn` | number \| null | Crates in |
| `purchasedBy` | string \| null | Purchased by |
| `receivedThrough` | string \| null | Received through |
| `vehicleNo` | string \| null | Vehicle number |
| `timeIn` | string \| null | Time in |
| `securityPerson` | string \| null | Security person |
| `deliveryReceivingPerson` | string \| null | Delivery receiving person |
| `rmn` | string \| null | RMN |
| `remark` | string \| null | Remark |
| `paymentInfo` | object \| null | Payment terms (see below) |

`paymentInfo` fields:

| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Payment info id |
| `paymentMode` | string \| null | Payment mode |
| `paymentDate` | string \| null | Payment date |
| `advancePaidAmt` | string \| null | Advance paid (decimal, serialised as a string) |
| `paymentTerms` | string \| null | Payment terms |
| `dueDate` | string \| null | Due date |
| `creditPeriod` | number \| null | Credit period |

**No matching records:** `200` with `data: []` and a total of `0`.

**Errors:** `400` invalid filter · `401` not authenticated · `500` unexpected error (see [§ 5](#5-error-responses)).

#### Export GRN

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/grns/export/excel` |
| **Purpose** | Downloads every GRN document matching the filters as an `.xlsx` workbook. Same filters, search, sort and visibility as Get All; **no pagination**. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | Same row visibility as Get All. |
| **Path parameters** | None |

**Query parameters:** every Get All parameter above; `page` and `limit` are ignored.

<details><summary>Full parameter list</summary>

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page`, `limit` | number | No | **Ignored** — the export always contains every matching record. | — |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `grns.createdAt` (GRN has no document date column) | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `grns.createdAt` (GRN has no document date column) | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `grnType` | enum (comma-separated) | No | GRN type. Exact match; any of: `transfer`, `purchase` | `transfer` |
| `purchaseType` | enum (comma-separated) | No | Purchase type. Exact match; any of: `fixed price sales`, `consignment sales / bikri`, `mgp sales` | `fixed price sales` |
| `locationType` | enum (comma-separated) | No | Location type. Exact match; any of: `cc`, `dc` | `cc` |
| `source` | enum (comma-separated) | No | Source: vendor or farmer. Exact match; any of: `vendor`, `farmer` | `vendor` |
| `paymentStatus` | enum (comma-separated) | No | Payment status. Exact match; any of: `paid`, `unpaid` | `paid` |
| `requestingDepartment` | enum (comma-separated) | No | Requesting department. Exact match; any of: `admin`, `hr`, `operations`, `sale`, `procurement`, `it`, `business_development`, `exports`, `branding_&_marketing`, `farming`, `quality_checking`, `other` | `admin` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `companyName` | uuid or string | No | Company id or name. UUID: exact id; otherwise partial, case-insensitive name | `Prime Fresh` |
| `dealSlipNo` | string | No | Deal slip number. Partial, case-insensitive | `DS2026` |
| `rfpaNo` | string | No | RFPA number. Partial, case-insensitive | `RFPA2026` |
| `vendorId` | uuid (comma-separated) | No | Vendor id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `vendorCode` | string | No | Vendor code. Partial, case-insensitive | `VEN001` |
| `vendorName` | string | No | Vendor name. Partial, case-insensitive | `Agro Traders` |
| `farmerId` | uuid (comma-separated) | No | Farmer id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `farmerCode` | string | No | Farmer code. Partial, case-insensitive | `FAR001` |
| `farmerName` | string | No | Farmer name. Partial, case-insensitive | `Suresh Patil` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: location, purchaseLocation, purchaseForSalesLocation. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `vehicleNo` | string | No | Vehicle number. Partial, case-insensitive | `MH12AB1234` |
| `billNo` | string | No | Bill number. Partial, case-insensitive | `B-101` |
| `minAmount` | number | No | GRN total amount >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | GRN total amount <= value. Inclusive upper bound | `50000` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |

</details>

**Example request**

```http
GET /grns/export/excel?startDate=2026-09-01&endDate=2026-09-15&status=COMPLETE&vendorId=9b2d4c1e-5f6a-4b7c-8d9e-0a1b2c3d4e5f&warehouseId=4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c&productId=1f2e3d4c-5b6a-4978-8a9b-0c1d2e3f4a5b
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/grns/export/excel?startDate=2026-09-01&endDate=2026-09-15&status=COMPLETE&vendorId=9b2d4c1e-5f6a-4b7c-8d9e-0a1b2c3d4e5f&warehouseId=4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c&productId=1f2e3d4c-5b6a-4978-8a9b-0c1d2e3f4a5b" \
  -H "Authorization: Bearer <token>" \
  --output GRN.xlsx
```

**Response `200`**

```text
Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Content-Disposition: attachment; filename="GRN_2026-09-15.xlsx"; filename*=UTF-8''GRN_2026-09-15.xlsx
Cache-Control: no-store
X-Export-Record-Count: <documents in the file>
```

File name pattern: `GRN_<YYYY-MM-DD>.xlsx`. Sheets:

| Sheet | Columns | Column headers (in order) |
|---|---|---|
| `GRN` | 85 | GRN No, GRN Record ID, GRN Type, Purchase Type, Location Type, Requesting Department, Source, Company, Company GST No, Deal Slip No, Deal Slip Lot No, RFPA No, Location, Base Location, Purchase Location, Other Purchase Location, Purchase For Sales Location, Other Purchase For Sales Location, Vendor ID, Vendor Name, Vendor Code, Vendor GSTN, Vendor Contact No, Farmer ID, Farmer Name, Farmer Code, Farmer Mobile No, Bill No, Bill Image, Sub Total Amount, Freight, Other Charges, Total Amount, Amount In Words, Amount Status, Vehicle No, Received Through, Crates In, Delivery Receiving Person, Security Person, RMN, Purchased By, Purchase By User, Purchase By User Employee ID, Purchase Instructions By, Purchase Instructions By Employee ID, Approval Note, Special Requirement, Current Level, Remark, Payment Mode, Payment Date, Advance Paid Amount, Remaining Amount, Payment Terms, Due Date, Credit Period, AQR Created, Inward Created, Dump Created, Customer DC Created, Multi Cash Voucher Created, Transport Voucher Created, Packing Material Voucher Created, Labour Voucher Created, Created By, Created By Employee ID, Created Date, Updated Date, Document ID, Overall Status, Last Approval Stage, Last Approval Action, Last Action By, Last Action Date, Approved By, Approval Date, Rejected By, Rejection Date, Rejection Reason, Document Remarks, Document Created By, Document Created By Employee ID, Document Created Date, Document Updated Date |
| `GRN Items` | 25 | GRN No, GRN Record ID, Item ID, Product ID, Product Code, Product Name, Category, Variant, Variant Code, UOM, Quantity, Revised Quantity, Unit Price, Revised Rate, Amount, Gross Weight, Packing Material Weight, Net Weight, Return To Vendor, Purchase Date, Expected Harvest Date, Dispatch Date, Delivery Date, Created Date, Updated Date |
| `GRN History` | 15 | GRN No, GRN Record ID, GRN Item ID, Version, Product ID, Product Code, Product Name, Variant, Old Quantity, New Quantity, Old Rate, New Rate, Modified By, Modified By Employee ID, Modified At |
| `Approvals` | 11 | GRN No, Record ID, Document ID, Overall Status, Stage, Stage Order, Action, Action By, Action By User ID, Reason, Action Date |

**Errors:** `400` invalid filter (JSON) · `401` not authenticated · `404 {"status":"fail","message":"No records found for the given filters"}` · `500` unexpected error.

#### Import GRN

Not available — no import endpoint exists for this module (see [§ 7](#7-import-apis)).

---

### 8.4 Inward Register

Document type `inward-register` · Record table: `inward_register` · Document number: `inwardNo`

#### Get All Inward Register

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/inwardRegister` |
| **Purpose** | Lists the Inward Register documents visible to the user, filtered, searched, sorted and paginated. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | No role check. Visible to the document creator, or a user in the approval flow’s **level-1 approver** block (any status). |
| **Implemented in** | `InwardRegisterController.getAllInwardRegisters → InwardRegisterService.getAllInwardRegisters` |
| **Path parameters** | None |

**Query parameters** (all optional)

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page` | number | No | Page number. Not applied (pre-existing behaviour): every filtered row is returned. `page` is echoed and `totalPages` is computed with `limit` (default 10). | `1` |
| `limit` | number | No | Records per page (see `page`). | `20` |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `inward_register.date` | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `inward_register.date` | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `inwardType` | enum (comma-separated) | No | Inward type. Exact match; any of: `purchase`, `transferred`, `returned-by-customer` | `purchase` |
| `source` | enum (comma-separated) | No | Source: vendor or farmer. Exact match; any of: `vendor`, `farmer` | `vendor` |
| `batchNo` | string | No | Batch number. Partial, case-insensitive | `BATCH01` |
| `grnNo` | string | No | GRN number. Partial, case-insensitive | `GRN2026` |
| `deliveryChallanNo` | string | No | Delivery challan number. Partial, case-insensitive | `CN2026` |
| `rbcNo` | string | No | Return by customer number. Partial, case-insensitive | `RBC2026` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: location, fromLocation. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `vendorId` | uuid (comma-separated) | No | Vendor id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `vendorCode` | string | No | Vendor code. Partial, case-insensitive | `VEN001` |
| `vendorName` | string | No | Vendor name. Partial, case-insensitive | `Agro Traders` |
| `farmerId` | uuid (comma-separated) | No | Farmer id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `farmerCode` | string | No | Farmer code. Partial, case-insensitive | `FAR001` |
| `farmerName` | string | No | Farmer name. Partial, case-insensitive | `Suresh Patil` |
| `customerId` | uuid (comma-separated) | No | Customer id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `customerCode` | string | No | Customer code. Partial, case-insensitive | `CUST001` |
| `customerName` | string | No | Customer name. Partial, case-insensitive | `ABC Foods` |
| `minAmount` | number | No | Inward cost >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Inward cost <= value. Inclusive upper bound | `50000` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |

**Search fields** (`search`): document number (`inwardNo`), creator name, document status, `batchNo`, GRN number, delivery challan number, company name, location name, vendor name, farmer name, customer name, product name / code.

**Sortable in the database** (`sort`): `createdAt`, `createdDate`, `updatedAt`, `status`, `overAllStatus`, `documentNo`, `inwardNo`, `date`.

**Example request**

```http
GET /inwardRegister?startDate=2026-09-01&endDate=2026-09-15&inwardType=purchase&locationId=4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c&productName=Onion
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/inwardRegister?startDate=2026-09-01&endDate=2026-09-15&inwardType=purchase&locationId=4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c&productName=Onion" \
  -H "Authorization: Bearer <token>"
```

**Response `200`**

| Field | Type | Description |
|---|---|---|
| `status` | string | `"success"` |
| `data` | object[] | The records (fields below) |
| `allRecords` | number | Total matching records |
| `totalPages` | number | Total pages |
| `page` | number | Current page |

```json
{
  "status": "success",
  "data": [
    {
      "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "documentId": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "overAllStatus": "hold",
      "createdBy": "string",
      "createdDate": "2026-09-15",
      "createdTime": "10:30 AM",
      "inwardNo": "string",
      "inwardType": "string",
      "date": "string",
      "batchNo": "string",
      "source": "string",
      "companyName": "string",
      "location": "string",
      "grnNo": "string",
      "deliveryChallanNo": "string",
      "rbcNo": "string",
      "vendorName": "string",
      "farmerName": "string",
      "incomingGrossQty": "0.00",
      "incomingNetQty": "0.00",
      "inwardGrossQty": "0.00",
      "inwardNetQty": "0.00",
      "inwardCost": "0.00",
      "totalWeightInKg": "0.00",
      "remarks": "string",
      "purchasedBy": "string",
      "inwardBy": "string"
    }
  ],
  "allRecords": 1,
  "totalPages": 1,
  "page": 1
}
```

`data[]` fields:

| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Record id of the module document |
| `documentId` | string (uuid) | Approval document id (`documents.id`), used by the `/view/:docid` endpoints |
| `overAllStatus` | string | Document workflow status (`documents.status`) |
| `createdBy` | string | Document creator's name |
| `createdDate` | string | Creation date, `YYYY-MM-DD` |
| `createdTime` | string | Creation time, `hh:mm A` |
| `inwardNo` | string | Inward number |
| `inwardType` | string | purchase, transferred or returned-by-customer |
| `date` | string \| null | Inward date |
| `batchNo` | string \| null | Batch number |
| `source` | string \| null | vendor or farmer |
| `companyName` | string \| null | Company name |
| `location` | string \| null | Location name |
| `grnNo` | string \| null | Linked GRN number |
| `deliveryChallanNo` | string \| null | Linked delivery challan number |
| `rbcNo` | string \| null | Linked return by customer number |
| `vendorName` | string \| null | Vendor name |
| `farmerName` | string \| null | Farmer name |
| `incomingGrossQty` | string \| null | Incoming gross quantity (decimal, serialised as a string) |
| `incomingNetQty` | string \| null | Incoming net quantity (decimal, serialised as a string) |
| `inwardGrossQty` | string \| null | Inward gross quantity (decimal, serialised as a string) |
| `inwardNetQty` | string \| null | Inward net quantity (decimal, serialised as a string) |
| `inwardCost` | string \| null | Inward cost (decimal, serialised as a string) |
| `totalWeightInKg` | string \| null | Total weight (kg) (decimal, serialised as a string) |
| `remarks` | string \| null | Remarks |
| `purchasedBy` | string \| null | Purchased by (name) |
| `inwardBy` | string \| null | Inward by (name) |

**No matching records:** `200` with `data: []` and a total of `0`.

**Errors:** `400` invalid filter · `401` not authenticated · `500` unexpected error (see [§ 5](#5-error-responses)).

#### Export Inward Register

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/inwardRegister/export/excel` |
| **Purpose** | Downloads every Inward Register document matching the filters as an `.xlsx` workbook. Same filters, search, sort and visibility as Get All; **no pagination**. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | Same row visibility as Get All. |
| **Path parameters** | None |

**Query parameters:** every Get All parameter above; `page` and `limit` are ignored.

<details><summary>Full parameter list</summary>

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page`, `limit` | number | No | **Ignored** — the export always contains every matching record. | — |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `inward_register.date` | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `inward_register.date` | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `inwardType` | enum (comma-separated) | No | Inward type. Exact match; any of: `purchase`, `transferred`, `returned-by-customer` | `purchase` |
| `source` | enum (comma-separated) | No | Source: vendor or farmer. Exact match; any of: `vendor`, `farmer` | `vendor` |
| `batchNo` | string | No | Batch number. Partial, case-insensitive | `BATCH01` |
| `grnNo` | string | No | GRN number. Partial, case-insensitive | `GRN2026` |
| `deliveryChallanNo` | string | No | Delivery challan number. Partial, case-insensitive | `CN2026` |
| `rbcNo` | string | No | Return by customer number. Partial, case-insensitive | `RBC2026` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: location, fromLocation. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `vendorId` | uuid (comma-separated) | No | Vendor id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `vendorCode` | string | No | Vendor code. Partial, case-insensitive | `VEN001` |
| `vendorName` | string | No | Vendor name. Partial, case-insensitive | `Agro Traders` |
| `farmerId` | uuid (comma-separated) | No | Farmer id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `farmerCode` | string | No | Farmer code. Partial, case-insensitive | `FAR001` |
| `farmerName` | string | No | Farmer name. Partial, case-insensitive | `Suresh Patil` |
| `customerId` | uuid (comma-separated) | No | Customer id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `customerCode` | string | No | Customer code. Partial, case-insensitive | `CUST001` |
| `customerName` | string | No | Customer name. Partial, case-insensitive | `ABC Foods` |
| `minAmount` | number | No | Inward cost >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Inward cost <= value. Inclusive upper bound | `50000` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |

</details>

**Example request**

```http
GET /inwardRegister/export/excel?startDate=2026-09-01&endDate=2026-09-15&inwardType=purchase&locationId=4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c&productName=Onion
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/inwardRegister/export/excel?startDate=2026-09-01&endDate=2026-09-15&inwardType=purchase&locationId=4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c&productName=Onion" \
  -H "Authorization: Bearer <token>" \
  --output Inward_Register.xlsx
```

**Response `200`**

```text
Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Content-Disposition: attachment; filename="Inward_Register_2026-09-15.xlsx"; filename*=UTF-8''Inward_Register_2026-09-15.xlsx
Cache-Control: no-store
X-Export-Record-Count: <documents in the file>
```

File name pattern: `Inward_Register_<YYYY-MM-DD>.xlsx`. Sheets:

| Sheet | Columns | Column headers (in order) |
|---|---|---|
| `Inward Register` | 57 | Inward No, Inward Record ID, Inward Type, Inward Date, Batch No, Source, Company, Company GST No, Location, From Location, GRN No, Delivery Challan No, Return By Customer No, Vendor ID, Vendor Name, Vendor Code, Vendor GSTN, Vendor Contact No, Farmer ID, Farmer Name, Farmer Code, Farmer Mobile No, Customer ID, Customer Name, Customer Code, Customer Contact No, Customer Email, Incoming Gross Qty, Incoming Net Qty, Inward Gross Qty, Inward Net Qty, Total Weight (Kg), Inward Cost, Remarks, Purchased By, Purchased By Employee ID, Inward By, Inward By Employee ID, Created Date, Updated Date, Document ID, Overall Status, Last Approval Stage, Last Approval Action, Last Action By, Last Action Date, Approved By, Approval Date, Rejected By, Rejection Date, Rejection Reason, Document Remarks, Document Created By, Document Created By Employee ID, Document Created Date, Document Updated Date, Inventory Posted |
| `Inward Items` | 20 | Inward No, Inward Record ID, Batch No, Item ID, Product ID, Product Code, Product Name, Category, Variant, Variant Code, UOM, Quantity, Weight, Packing Material Weight, Gross Weight, Net Weight, Unit Price, Amount, Created Date, Updated Date |
| `Approvals` | 11 | Inward No, Record ID, Document ID, Overall Status, Stage, Stage Order, Action, Action By, Action By User ID, Reason, Action Date |

**Errors:** `400` invalid filter (JSON) · `401` not authenticated · `404 {"status":"fail","message":"No records found for the given filters"}` · `500` unexpected error.

#### Import Inward Register

Not available — no import endpoint exists for this module (see [§ 7](#7-import-apis)).

---

### 8.5 AQR

Document type `aqr` · Record table: `aqr` · Document number: `aqrNo`

#### Get All AQR

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/aqr` |
| **Purpose** | Lists the AQR documents visible to the user, filtered, searched, sorted and paginated. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | No role check. Visible to the document creator, or a user in the approval flow’s **level-1 approver** block (any status). |
| **Implemented in** | `AqrController (GET /) → AqrService.getAllAqrs` |
| **Path parameters** | None |

**Query parameters** (all optional)

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page` | number | No | Page number. Not applied (pre-existing behaviour): every filtered row is returned. `page` is echoed and `totalPages` is computed with `limit` (default 10). | `1` |
| `limit` | number | No | Records per page (see `page`). | `20` |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `aqr.arrivalDate` | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `aqr.arrivalDate` | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `aqrFor` | enum (comma-separated) | No | AQR for. Exact match; any of: `purchase`, `transfer` | `purchase` |
| `source` | enum (comma-separated) | No | Source: vendor or farmer. Exact match; any of: `vendor`, `farmer` | `vendor` |
| `deliveryChallanNo` | string | No | Delivery challan number. Partial, case-insensitive | `CN2026` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: location, fromLocation. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `vendorId` | uuid (comma-separated) | No | Vendor id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `vendorCode` | string | No | Vendor code. Partial, case-insensitive | `VEN001` |
| `vendorName` | string | No | Vendor name. Partial, case-insensitive | `Agro Traders` |
| `farmerId` | uuid (comma-separated) | No | Farmer id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `farmerCode` | string | No | Farmer code. Partial, case-insensitive | `FAR001` |
| `farmerName` | string | No | Farmer name. Partial, case-insensitive | `Suresh Patil` |
| `supplierName` | string | No | Vendor or farmer name. Partial, case-insensitive | `Agro` |
| `arrivalDate` | date `YYYY-MM-DD` | No | Exact arrival date (YYYY-MM-DD). Exact day | `2026-09-10` |
| `minQuantity` | number | No | Total quantity >= value. Inclusive lower bound | `10` |
| `maxQuantity` | number | No | Total quantity <= value. Inclusive upper bound | `500` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on the product on the record | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on the product on the record. Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on the product on the record. Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on the product on the record | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on the product on the record. Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on the product on the record | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on the product on the record. Partial, case-insensitive | `Vegetables` |

**Search fields** (`search`): document number (`aqrNo`), creator name, document status, delivery challan number, company name, location name, vendor name, farmer name, product name, product code.

**Sortable in the database** (`sort`): `createdAt`, `createdDate`, `updatedAt`, `status`, `overAllStatus`, `documentNo`, `aqrNo`, `arrivalDate`.

**Example request**

```http
GET /aqr?startDate=2026-09-01&endDate=2026-09-15&aqrFor=purchase&supplierName=Agro&productId=1f2e3d4c-5b6a-4978-8a9b-0c1d2e3f4a5b
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/aqr?startDate=2026-09-01&endDate=2026-09-15&aqrFor=purchase&supplierName=Agro&productId=1f2e3d4c-5b6a-4978-8a9b-0c1d2e3f4a5b" \
  -H "Authorization: Bearer <token>"
```

**Response `200`**

| Field | Type | Description |
|---|---|---|
| `status` | string | `"success"` |
| `data` | object[] | The records (fields below) |
| `allRecords` | number | Total matching records |
| `totalPages` | number | Total pages |
| `page` | number | Current page |

```json
{
  "status": "success",
  "data": [
    {
      "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "documentId": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "overAllStatus": "hold",
      "createdBy": "string",
      "createdDate": "2026-09-15",
      "createdTime": "10:30 AM",
      "aqrNo": "string",
      "aqrFor": "string",
      "source": "string",
      "companyName": "string",
      "location": "string",
      "fromLocation": "string",
      "deliveryChallanNo": "string",
      "selectedParty": "string",
      "product": "string",
      "variant": "string",
      "arrivalDate": "string",
      "arrivedQty": "string",
      "samplingQty": "string",
      "totalQty": "0.00",
      "totalpercent": "0.00",
      "purchaseBy": "string",
      "receivedBy": "string",
      "qcCheckBy": "string",
      "verifiedBy": "string",
      "remark": "string"
    }
  ],
  "allRecords": 1,
  "totalPages": 1,
  "page": 1
}
```

`data[]` fields:

| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Record id of the module document |
| `documentId` | string (uuid) | Approval document id (`documents.id`), used by the `/view/:docid` endpoints |
| `overAllStatus` | string | Document workflow status (`documents.status`) |
| `createdBy` | string | Document creator's name |
| `createdDate` | string | Creation date, `YYYY-MM-DD` |
| `createdTime` | string | Creation time, `hh:mm A` |
| `aqrNo` | string | AQR number |
| `aqrFor` | string | purchase or transfer |
| `source` | string \| null | vendor or farmer |
| `companyName` | string \| null | Company name |
| `location` | string \| null | Location name |
| `fromLocation` | string \| null | From location name |
| `deliveryChallanNo` | string \| null | Linked delivery challan number |
| `selectedParty` | string \| null | Vendor or farmer name |
| `product` | string \| null | Product name |
| `variant` | string \| null | Variant name |
| `arrivalDate` | string \| null | Arrival date |
| `arrivedQty` | string \| null | Arrived quantity (text column) |
| `samplingQty` | string \| null | Sampling quantity (text column) |
| `totalQty` | string \| null | Total quantity (decimal, serialised as a string) |
| `totalpercent` | string \| null | Total percent (decimal, serialised as a string) |
| `purchaseBy` | string \| null | Purchase by (name) |
| `receivedBy` | string \| null | Received by (name) |
| `qcCheckBy` | string \| null | QC check by (name) |
| `verifiedBy` | string \| null | Verified by (name) |
| `remark` | string \| null | Remark |

**No matching records:** `200` with `data: []` and a total of `0`.

**Errors:** `400` invalid filter · `401` not authenticated · `500` unexpected error (see [§ 5](#5-error-responses)).

#### Export AQR

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/aqr/export/excel` |
| **Purpose** | Downloads every AQR document matching the filters as an `.xlsx` workbook. Same filters, search, sort and visibility as Get All; **no pagination**. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | Same row visibility as Get All. |
| **Path parameters** | None |

**Query parameters:** every Get All parameter above; `page` and `limit` are ignored.

<details><summary>Full parameter list</summary>

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page`, `limit` | number | No | **Ignored** — the export always contains every matching record. | — |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `aqr.arrivalDate` | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `aqr.arrivalDate` | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `aqrFor` | enum (comma-separated) | No | AQR for. Exact match; any of: `purchase`, `transfer` | `purchase` |
| `source` | enum (comma-separated) | No | Source: vendor or farmer. Exact match; any of: `vendor`, `farmer` | `vendor` |
| `deliveryChallanNo` | string | No | Delivery challan number. Partial, case-insensitive | `CN2026` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: location, fromLocation. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `vendorId` | uuid (comma-separated) | No | Vendor id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `vendorCode` | string | No | Vendor code. Partial, case-insensitive | `VEN001` |
| `vendorName` | string | No | Vendor name. Partial, case-insensitive | `Agro Traders` |
| `farmerId` | uuid (comma-separated) | No | Farmer id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `farmerCode` | string | No | Farmer code. Partial, case-insensitive | `FAR001` |
| `farmerName` | string | No | Farmer name. Partial, case-insensitive | `Suresh Patil` |
| `supplierName` | string | No | Vendor or farmer name. Partial, case-insensitive | `Agro` |
| `arrivalDate` | date `YYYY-MM-DD` | No | Exact arrival date (YYYY-MM-DD). Exact day | `2026-09-10` |
| `minQuantity` | number | No | Total quantity >= value. Inclusive lower bound | `10` |
| `maxQuantity` | number | No | Total quantity <= value. Inclusive upper bound | `500` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on the product on the record | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on the product on the record. Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on the product on the record. Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on the product on the record | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on the product on the record. Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on the product on the record | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on the product on the record. Partial, case-insensitive | `Vegetables` |

</details>

**Example request**

```http
GET /aqr/export/excel?startDate=2026-09-01&endDate=2026-09-15&aqrFor=purchase&supplierName=Agro&productId=1f2e3d4c-5b6a-4978-8a9b-0c1d2e3f4a5b
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/aqr/export/excel?startDate=2026-09-01&endDate=2026-09-15&aqrFor=purchase&supplierName=Agro&productId=1f2e3d4c-5b6a-4978-8a9b-0c1d2e3f4a5b" \
  -H "Authorization: Bearer <token>" \
  --output AQR.xlsx
```

**Response `200`**

```text
Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Content-Disposition: attachment; filename="AQR_2026-09-15.xlsx"; filename*=UTF-8''AQR_2026-09-15.xlsx
Cache-Control: no-store
X-Export-Record-Count: <documents in the file>
```

File name pattern: `AQR_<YYYY-MM-DD>.xlsx`. Sheets:

| Sheet | Columns | Column headers (in order) |
|---|---|---|
| `AQR` | 56 | AQR No, AQR Record ID, AQR For, Source, Company, Company GST No, Location, From Location, Delivery Challan No, Vendor ID, Vendor Name, Vendor Code, Vendor GSTN, Vendor Contact No, Farmer ID, Farmer Name, Farmer Code, Farmer Mobile No, Product ID, Product Code, Product Name, Category, Variant, Variant Code, Arrival Date, Arrived Qty, Sampling Qty, Total Qty, Total Percent, Purchase By, Purchase By Employee ID, Received By, Received By Employee ID, QC Check By, QC Check By Employee ID, Verified By, Verified By Employee ID, Remark, Created Date, Updated Date, Document ID, Overall Status, Last Approval Stage, Last Approval Action, Last Action By, Last Action Date, Approved By, Approval Date, Rejected By, Rejection Date, Rejection Reason, Document Remarks, Document Created By, Document Created By Employee ID, Document Created Date, Document Updated Date |
| `AQR Quality Parameters` | 10 | AQR No, AQR Record ID, Parameter Row ID, Quality Parameter ID, Quality Parameter, Parameter Type, Quantity, Percentage, Created Date, Updated Date |
| `Approvals` | 11 | AQR No, Record ID, Document ID, Overall Status, Stage, Stage Order, Action, Action By, Action By User ID, Reason, Action Date |

**Errors:** `400` invalid filter (JSON) · `401` not authenticated · `404 {"status":"fail","message":"No records found for the given filters"}` · `500` unexpected error.

#### Import AQR

Not available — no import endpoint exists for this module (see [§ 7](#7-import-apis)).

---

### 8.6 Dump Register

Document type `dump-register` · Record table: `dump_register` · Document number: `dumpNo`

#### Get All Dump Register

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/dumpRegister` |
| **Purpose** | Lists the Dump Register documents visible to the user, filtered, searched, sorted and paginated. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | No role check. Visible to the document creator, or a user in the **level-1 or level-2 approver** block (any status). |
| **Implemented in** | `DumpRegisterController (GET /) → DumpRegisterService.getAllDumpRegisters` |
| **Path parameters** | None |

**Query parameters** (all optional)

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page` | number | No | Page number. When absent, page 1 and limit 10 are used (SQL `OFFSET`/`LIMIT`); always paginated. | `1` |
| `limit` | number | No | Records per page (see `page`). | `20` |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `dump_register.date` | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `dump_register.date` | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `dumpType` | enum (comma-separated) | No | Dump type. Exact match; any of: `purchase`, `transferred`, `returned-by-customer` | `purchase` |
| `batchNo` | string | No | Batch number. Partial, case-insensitive | `BATCH01` |
| `grnNo` | string | No | GRN number. Partial, case-insensitive | `GRN2026` |
| `deliveryChallanNo` | string | No | Delivery challan number. Partial, case-insensitive | `CN2026` |
| `rbcNo` | string | No | Return by customer number. Partial, case-insensitive | `RBC2026` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: location. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `requestedById` | uuid (comma-separated) | No | Requested-by user id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `minAmount` | number | No | Total dump cost >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Total dump cost <= value. Inclusive upper bound | `50000` |
| `minQuantity` | number | No | Total dump quantity >= value. Inclusive lower bound | `10` |
| `maxQuantity` | number | No | Total dump quantity <= value. Inclusive upper bound | `500` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |

**Search fields** (`search`): document number (`dumpNo`), creator name, document status, `batchNo`, GRN number, delivery challan number, return by customer number, company name, location name, `remark`, product name / code.

**Sortable in the database** (`sort`): `createdAt`, `createdDate`, `updatedAt`, `status`, `overAllStatus`, `documentNo`, `dumpNo`, `date`.

**Example request**

```http
GET /dumpRegister?startDate=2026-09-01&endDate=2026-09-15&dumpType=purchase&locationId=4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c&minAmount=1000&page=1&limit=20
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/dumpRegister?startDate=2026-09-01&endDate=2026-09-15&dumpType=purchase&locationId=4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c&minAmount=1000&page=1&limit=20" \
  -H "Authorization: Bearer <token>"
```

**Response `200`**

| Field | Type | Description |
|---|---|---|
| `status` | string | `"success"` |
| `data` | object[] | The records (fields below) |
| `allRecords` | number | Total matching records |
| `totalPages` | number | Total pages |
| `page` | number | Current page |

```json
{
  "status": "success",
  "data": [
    {
      "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "documentId": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "overAllStatus": "hold",
      "createdBy": "string",
      "createdDate": "2026-09-15",
      "createdTime": "10:30 AM",
      "dumpNo": "string",
      "dumpType": "string",
      "date": "string",
      "batchNo": "string",
      "companyName": "string",
      "location": "string",
      "grn": "string",
      "deliveryChallanNo": "string",
      "rbcNo": "string",
      "totalQty": 0,
      "totalDumpCost": 0,
      "totalCostInWords": "string",
      "remark": "string"
    }
  ],
  "allRecords": 1,
  "totalPages": 1,
  "page": 1
}
```

`data[]` fields:

| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Record id of the module document |
| `documentId` | string (uuid) | Approval document id (`documents.id`), used by the `/view/:docid` endpoints |
| `overAllStatus` | string | Document workflow status (`documents.status`) |
| `createdBy` | string | Document creator's name |
| `createdDate` | string | Creation date, `YYYY-MM-DD` |
| `createdTime` | string | Creation time, `hh:mm A` |
| `dumpNo` | string | Dump number |
| `dumpType` | string | purchase, transferred or returned-by-customer |
| `date` | string \| null | Dump date |
| `batchNo` | string \| null | Batch number |
| `companyName` | string \| null | Company name |
| `location` | string \| null | Location name |
| `grn` | string \| null | Linked GRN number |
| `deliveryChallanNo` | string \| null | Linked delivery challan number |
| `rbcNo` | string \| null | Linked return by customer number |
| `totalQty` | number \| null | Total dump quantity |
| `totalDumpCost` | number \| null | Total dump cost |
| `totalCostInWords` | string \| null | Total cost in words |
| `remark` | string \| null | Remark |

**No matching records:** `200` with `data: []` and a total of `0`.

**Errors:** `400` invalid filter · `401` not authenticated · `500` unexpected error (see [§ 5](#5-error-responses)).

#### Export Dump Register

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/dumpRegister/export/excel` |
| **Purpose** | Downloads every Dump Register document matching the filters as an `.xlsx` workbook. Same filters, search, sort and visibility as Get All; **no pagination**. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | Same row visibility as Get All. |
| **Path parameters** | None |

**Query parameters:** every Get All parameter above; `page` and `limit` are ignored.

<details><summary>Full parameter list</summary>

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page`, `limit` | number | No | **Ignored** — the export always contains every matching record. | — |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `dump_register.date` | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `dump_register.date` | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `dumpType` | enum (comma-separated) | No | Dump type. Exact match; any of: `purchase`, `transferred`, `returned-by-customer` | `purchase` |
| `batchNo` | string | No | Batch number. Partial, case-insensitive | `BATCH01` |
| `grnNo` | string | No | GRN number. Partial, case-insensitive | `GRN2026` |
| `deliveryChallanNo` | string | No | Delivery challan number. Partial, case-insensitive | `CN2026` |
| `rbcNo` | string | No | Return by customer number. Partial, case-insensitive | `RBC2026` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: location. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `requestedById` | uuid (comma-separated) | No | Requested-by user id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `minAmount` | number | No | Total dump cost >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Total dump cost <= value. Inclusive upper bound | `50000` |
| `minQuantity` | number | No | Total dump quantity >= value. Inclusive lower bound | `10` |
| `maxQuantity` | number | No | Total dump quantity <= value. Inclusive upper bound | `500` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |

</details>

**Example request**

```http
GET /dumpRegister/export/excel?startDate=2026-09-01&endDate=2026-09-15&dumpType=purchase&locationId=4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c&minAmount=1000
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/dumpRegister/export/excel?startDate=2026-09-01&endDate=2026-09-15&dumpType=purchase&locationId=4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c&minAmount=1000" \
  -H "Authorization: Bearer <token>" \
  --output Dump_Register.xlsx
```

**Response `200`**

```text
Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Content-Disposition: attachment; filename="Dump_Register_2026-09-15.xlsx"; filename*=UTF-8''Dump_Register_2026-09-15.xlsx
Cache-Control: no-store
X-Export-Record-Count: <documents in the file>
```

File name pattern: `Dump_Register_<YYYY-MM-DD>.xlsx`. Sheets:

| Sheet | Columns | Column headers (in order) |
|---|---|---|
| `Dump Register` | 36 | Dump No, Dump Record ID, Dump Type, Dump Date, Batch No, Company, Company GST No, Location, GRN No, Delivery Challan No, Return By Customer No, Total Dump Qty, Total Dump Cost, Total Cost In Words, Remark, Requested By, Requested By Employee ID, Created Date, Updated Date, Document ID, Overall Status, Last Approval Stage, Last Approval Action, Last Action By, Last Action Date, Approved By, Approval Date, Rejected By, Rejection Date, Rejection Reason, Document Remarks, Document Created By, Document Created By Employee ID, Document Created Date, Document Updated Date, Inventory Posted |
| `Dump Items` | 16 | Dump No, Dump Record ID, Batch No, Item ID, Product ID, Product Code, Product Name, Category, Variant, Variant Code, UOM, Dump Quantity, Unit Price, Amount, Created Date, Updated Date |
| `Approvals` | 11 | Dump No, Record ID, Document ID, Overall Status, Stage, Stage Order, Action, Action By, Action By User ID, Reason, Action Date |

**Errors:** `400` invalid filter (JSON) · `401` not authenticated · `404 {"status":"fail","message":"No records found for the given filters"}` · `500` unexpected error.

#### Import Dump Register

Not available — no import endpoint exists for this module (see [§ 7](#7-import-apis)).

---

### 8.7 Delivery Challan – Customer

Document type `DC_TYPE_CUSTOMER` · Record table: `delivery_challan_purchase` · Document number: `challanNo`

#### Get All Delivery Challan – Customer

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/customer-delivery-challan` |
| **Purpose** | Lists the Delivery Challan – Customer documents visible to the user, filtered, searched, sorted and paginated. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | No role check. Visible to the document creator, or a user in the **level-1 or level-2 approver** block (any status). |
| **Implemented in** | `CustomerDeliveryChallanController (GET /) → CustomerDeliveryChallanService.getAllCustomerDeliveryChallans` |
| **Path parameters** | None |

**Query parameters** (all optional)

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page` | number | No | Page number. When absent, page 1 and limit 10 are used (SQL `OFFSET`/`LIMIT`); always paginated. | `1` |
| `limit` | number | No | Records per page (see `page`). | `20` |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `delivery_challan_purchase.createdAt` (no challan date column) | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `delivery_challan_purchase.createdAt` (no challan date column) | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `approvalStatus` | enum (comma-separated) | No | Challan approval status. Exact match; any of: `pending`, `approved`, `notapproved`, `active`, `incomplete`, `draft` | `pending` |
| `requestingDepartment` | enum (comma-separated) | No | Requesting department. Exact match; any of: `admin`, `hr`, `operations`, `sale`, `procurement`, `it`, `business_development`, `exports`, `branding_&_marketing`, `farming`, `quality_checking`, `other` | `admin` |
| `grnNo` | string | No | Linked GRN number. Partial, case-insensitive | `GRN2026` |
| `vehicleNo` | string | No | Vehicle number. Partial, case-insensitive | `MH12AB1234` |
| `driverName` | string | No | Driver name. Partial, case-insensitive | `Ramesh` |
| `isReturned` | boolean | No | Challan has been returned. `true` / `false` (also `1`/`0`, `yes`/`no`) | `true` |
| `minAmount` | number | No | Total product amount >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Total product amount <= value. Inclusive upper bound | `50000` |
| `customerId` | uuid (comma-separated) | No | Customer id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `customerCode` | string | No | Customer code. Partial, case-insensitive | `CUST001` |
| `customerName` | string | No | Customer name. Partial, case-insensitive | `ABC Foods` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: fromLocation. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `poNumber` | string | No | PO number. Partial, case-insensitive | `PO-778` |
| `isInvoiceCreated` | boolean | No | An invoice has been raised. `true` / `false` (also `1`/`0`, `yes`/`no`) | `true` |
| `isReturnByCustomerCreated` | boolean | No | A return by customer has been raised. `true` / `false` (also `1`/`0`, `yes`/`no`) | `true` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |

**Search fields** (`search`): document number (`challanNo`), creator name, document status, company name, GRN number, `vehicleNo`, `driverName`, `receiverName`, customer name, customer code, `poNumber`, from location name, product name / code.

**Sortable in the database** (`sort`): `createdAt`, `createdDate`, `updatedAt`, `status`, `overAllStatus`, `documentNo`, `challanNo`.

**Example request**

```http
GET /customer-delivery-challan?startDate=2026-09-01&endDate=2026-09-15&customerId=7a8b9c0d-1e2f-4a3b-8c4d-5e6f7a8b9c0d&vehicleNo=MH12&productName=Tomato&page=1&limit=20
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/customer-delivery-challan?startDate=2026-09-01&endDate=2026-09-15&customerId=7a8b9c0d-1e2f-4a3b-8c4d-5e6f7a8b9c0d&vehicleNo=MH12&productName=Tomato&page=1&limit=20" \
  -H "Authorization: Bearer <token>"
```

**Response `200`**

| Field | Type | Description |
|---|---|---|
| `status` | string | `"success"` |
| `data` | object[] | The records (fields below) |
| `allRecords` | number | Total matching records |
| `totalPages` | number | Total pages |
| `page` | number | Current page |

```json
{
  "status": "success",
  "data": [
    {
      "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "documentId": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "overAllStatus": "hold",
      "createdBy": "string",
      "createdDate": "2026-09-15",
      "createdTime": "10:30 AM",
      "challanNo": "string",
      "poNumber": "string",
      "customerName": "string",
      "companyName": {},
      "fromLocation": {},
      "billingAddress": {
        "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
        "address1": "string",
        "address2": "string",
        "location": "string",
        "city": "string",
        "state": "string",
        "pincode": "string"
      },
      "deliveryAddress": {
        "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
        "address1": "string",
        "address2": "string",
        "location": "string",
        "city": "string",
        "state": "string",
        "pincode": "string"
      },
      "currentShippingAddress": {
        "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
        "address1": "string",
        "address2": "string",
        "location": "string",
        "city": "string",
        "state": "string",
        "pincode": "string"
      },
      "office": "string",
      "grnNo": "string",
      "transitInsuranceNo": "string",
      "totalProductAmount": "0.00",
      "netProductWeight": "0.00",
      "netPackagingMaterialWeight": "0.00",
      "totalPackagingMaterialAmount": "0.00",
      "totalAmtInWords": "string",
      "driverName": "string",
      "licenseNo": "string",
      "contactNo": "string",
      "altContactNo": "string",
      "vehicleNo": "string",
      "receiverName": "string",
      "rmn": "string",
      "remark": "string",
      "anyAttachment": [],
      "deliveryChallanProducts": [
        {
          "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
          "unitPrice": "0.00",
          "amount": "0.00",
          "grossWeight": "0.00",
          "netWeight": "0.00",
          "packagingMaterialQuantity": 0,
          "packagingMaterialUnitPrice": 0,
          "packagingMaterialAmount": 0,
          "packagingMaterialTotalWeight": 0
        }
      ]
    }
  ],
  "allRecords": 1,
  "totalPages": 1,
  "page": 1
}
```

`data[]` fields:

| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Record id of the module document |
| `documentId` | string (uuid) | Approval document id (`documents.id`), used by the `/view/:docid` endpoints |
| `overAllStatus` | string | Document workflow status (`documents.status`) |
| `createdBy` | string | Document creator's name |
| `createdDate` | string | Creation date, `YYYY-MM-DD` |
| `createdTime` | string | Creation time, `hh:mm A` |
| `challanNo` | string | Challan number |
| `poNumber` | string \| null | PO number |
| `customerName` | string \| null | Customer name |
| `companyName` | object \| null | `{ id, name }` of the company |
| `fromLocation` | object \| null | `{ id, name }` of the dispatch location |
| `billingAddress` | object \| null | Address (see below) |
| `deliveryAddress` | object \| null | Address (see below) |
| `currentShippingAddress` | object \| null | Address (see below) |
| `office` | string \| null | Office name |
| `grnNo` | string \| null | Linked GRN number |
| `transitInsuranceNo` | string \| null | Transit insurance number |
| `totalProductAmount` | string \| null | Total product amount (decimal, serialised as a string) |
| `netProductWeight` | string \| null | Net product weight (decimal, serialised as a string) |
| `netPackagingMaterialWeight` | string \| null | Net packaging material weight (decimal, serialised as a string) |
| `totalPackagingMaterialAmount` | string \| null | Total packaging material amount (decimal, serialised as a string) |
| `totalAmtInWords` | string \| null | Total amount in words |
| `driverName` | string \| null | Driver name |
| `licenseNo` | string \| null | Driver license number |
| `contactNo` | string \| null | Contact number |
| `altContactNo` | string \| null | Alternate contact number |
| `vehicleNo` | string \| null | Vehicle number |
| `receiverName` | string \| null | Receiver name |
| `rmn` | string \| null | RMN |
| `remark` | string \| null | Remark |
| `anyAttachment` | string[] \| null | Attachment URLs |
| `deliveryChallanProducts` | object[] | Challan lines (see below) |

`billingAddress / deliveryAddress / currentShippingAddress` fields:

| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Address id |
| `address1` | string | Address line 1 |
| `address2` | string \| null | Address line 2 |
| `location` | string \| null | Locality |
| `city` | string | City |
| `state` | string | State |
| `pincode` | string | PIN code |

`deliveryChallanProducts` fields:

| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Line id |
| `unitPrice` | string | Unit price (decimal, serialised as a string) |
| `amount` | string | Amount (decimal, serialised as a string) |
| `grossWeight` | string | Gross weight (decimal, serialised as a string) |
| `netWeight` | string | Net weight (decimal, serialised as a string) |
| `packagingMaterialQuantity` | number \| null | Packaging material quantity |
| `packagingMaterialUnitPrice` | number \| null | Packaging material unit price |
| `packagingMaterialAmount` | number \| null | Packaging material amount |
| `packagingMaterialTotalWeight` | number \| null | Packaging material total weight |

**No matching records:** `404 {"status":"fail","message":"No customer delivery challans found"}`.

**Errors:** `400` invalid filter · `401` not authenticated · `500` unexpected error (see [§ 5](#5-error-responses)).

#### Export Delivery Challan – Customer

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/customer-delivery-challan/export/excel` |
| **Purpose** | Downloads every Delivery Challan – Customer document matching the filters as an `.xlsx` workbook. Same filters, search, sort and visibility as Get All; **no pagination**. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | Same row visibility as Get All. |
| **Path parameters** | None |

**Query parameters:** every Get All parameter above; `page` and `limit` are ignored.

<details><summary>Full parameter list</summary>

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page`, `limit` | number | No | **Ignored** — the export always contains every matching record. | — |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `delivery_challan_purchase.createdAt` (no challan date column) | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `delivery_challan_purchase.createdAt` (no challan date column) | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `approvalStatus` | enum (comma-separated) | No | Challan approval status. Exact match; any of: `pending`, `approved`, `notapproved`, `active`, `incomplete`, `draft` | `pending` |
| `requestingDepartment` | enum (comma-separated) | No | Requesting department. Exact match; any of: `admin`, `hr`, `operations`, `sale`, `procurement`, `it`, `business_development`, `exports`, `branding_&_marketing`, `farming`, `quality_checking`, `other` | `admin` |
| `grnNo` | string | No | Linked GRN number. Partial, case-insensitive | `GRN2026` |
| `vehicleNo` | string | No | Vehicle number. Partial, case-insensitive | `MH12AB1234` |
| `driverName` | string | No | Driver name. Partial, case-insensitive | `Ramesh` |
| `isReturned` | boolean | No | Challan has been returned. `true` / `false` (also `1`/`0`, `yes`/`no`) | `true` |
| `minAmount` | number | No | Total product amount >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Total product amount <= value. Inclusive upper bound | `50000` |
| `customerId` | uuid (comma-separated) | No | Customer id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `customerCode` | string | No | Customer code. Partial, case-insensitive | `CUST001` |
| `customerName` | string | No | Customer name. Partial, case-insensitive | `ABC Foods` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: fromLocation. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `poNumber` | string | No | PO number. Partial, case-insensitive | `PO-778` |
| `isInvoiceCreated` | boolean | No | An invoice has been raised. `true` / `false` (also `1`/`0`, `yes`/`no`) | `true` |
| `isReturnByCustomerCreated` | boolean | No | A return by customer has been raised. `true` / `false` (also `1`/`0`, `yes`/`no`) | `true` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |

</details>

**Example request**

```http
GET /customer-delivery-challan/export/excel?startDate=2026-09-01&endDate=2026-09-15&customerId=7a8b9c0d-1e2f-4a3b-8c4d-5e6f7a8b9c0d&vehicleNo=MH12&productName=Tomato
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/customer-delivery-challan/export/excel?startDate=2026-09-01&endDate=2026-09-15&customerId=7a8b9c0d-1e2f-4a3b-8c4d-5e6f7a8b9c0d&vehicleNo=MH12&productName=Tomato" \
  -H "Authorization: Bearer <token>" \
  --output Customer_Delivery_Challan.xlsx
```

**Response `200`**

```text
Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Content-Disposition: attachment; filename="Customer_Delivery_Challan_2026-09-15.xlsx"; filename*=UTF-8''Customer_Delivery_Challan_2026-09-15.xlsx
Cache-Control: no-store
X-Export-Record-Count: <documents in the file>
```

File name pattern: `Customer_Delivery_Challan_<YYYY-MM-DD>.xlsx`. Sheets:

| Sheet | Columns | Column headers (in order) |
|---|---|---|
| `Customer DC` | 59 | Challan No, Challan Record ID, Customer ID, Customer Name, Customer Code, Customer Contact No, Customer Email, PO Number, From Location, Billing Address, Delivery Address, Current Shipping Address, Invoice Created, Invoice Nos, Return By Customer Created, Return By Customer Nos, Company, Company GST No, Office, GRN No, Requesting Department, Challan Approval Status, Transit Insurance No, Vehicle No, Driver Name, Driver License No, Contact No, Alternate Contact No, RMN, Receiver Name, Total Product Amount, Net Product Weight, Net Packaging Material Weight, Total Packaging Material Amount, Total Amount In Words, Returned, Attachments, Remark, Created By, Created By Employee ID, Created Date, Updated Date, Document ID, Overall Status, Last Approval Stage, Last Approval Action, Last Action By, Last Action Date, Approved By, Approval Date, Rejected By, Rejection Date, Rejection Reason, Document Remarks, Document Created By, Document Created By Employee ID, Document Created Date, Document Updated Date, Inventory Posted |
| `Customer DC Items` | 31 | Challan No, Challan Record ID, Item ID, Product ID, Product Code, Product Name, Category, Variant, Variant Code, UOM, Sale UOM, Quantity, Accepted Qty, Rejected Qty, Returned Qty, Changed Qty, Unit Price, Changed Price, Amount, Gross Weight, Packing Material Weight, Net Weight, Packing Material Quantity, Packaging Material, Packaging Material UOM, Packaging Material Quantity, Packaging Material Unit Price, Packaging Material Amount, Packaging Material Total Weight, Created Date, Updated Date |
| `Approvals` | 11 | Challan No, Record ID, Document ID, Overall Status, Stage, Stage Order, Action, Action By, Action By User ID, Reason, Action Date |

**Errors:** `400` invalid filter (JSON) · `401` not authenticated · `404 {"status":"fail","message":"No records found for the given filters"}` · `500` unexpected error.

#### Import Delivery Challan – Customer

Not available — no import endpoint exists for this module (see [§ 7](#7-import-apis)).

---

### 8.8 Delivery Challan – Stock Transfer

Document type `DC_TYPE_STOCK_TRANSFER` · Record table: `delivery_challan_purchase` · Document number: `challanNo`

#### Get All Delivery Challan – Stock Transfer

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/tranfer-delivery-challan` |
| **Purpose** | Lists the Delivery Challan – Stock Transfer documents visible to the user, filtered, searched, sorted and paginated. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | No role check. Visible to the document creator, or a user in the **level-1 or level-2 approver** block (any status). |
| **Implemented in** | `StockTransferDeliveryChallanController (GET /) → StockTransferDeliveryChallanService.getAll` |
| **Path parameters** | None |

**Query parameters** (all optional)

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page` | number | No | Page number. When absent, page 1 and limit 10 are used (SQL `OFFSET`/`LIMIT`); always paginated. | `1` |
| `limit` | number | No | Records per page (see `page`). | `20` |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `delivery_challan_purchase.createdAt` (no challan date column) | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `delivery_challan_purchase.createdAt` (no challan date column) | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `approvalStatus` | enum (comma-separated) | No | Challan approval status. Exact match; any of: `pending`, `approved`, `notapproved`, `active`, `incomplete`, `draft` | `pending` |
| `requestingDepartment` | enum (comma-separated) | No | Requesting department. Exact match; any of: `admin`, `hr`, `operations`, `sale`, `procurement`, `it`, `business_development`, `exports`, `branding_&_marketing`, `farming`, `quality_checking`, `other` | `admin` |
| `grnNo` | string | No | Linked GRN number. Partial, case-insensitive | `GRN2026` |
| `vehicleNo` | string | No | Vehicle number. Partial, case-insensitive | `MH12AB1234` |
| `driverName` | string | No | Driver name. Partial, case-insensitive | `Ramesh` |
| `isReturned` | boolean | No | Challan has been returned. `true` / `false` (also `1`/`0`, `yes`/`no`) | `true` |
| `minAmount` | number | No | Total product amount >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Total product amount <= value. Inclusive upper bound | `50000` |
| `stockTransferType` | enum (comma-separated) | No | Stock transfer type. Exact match; any of: `cc-dc stock transfer`, `dc-dc stock transfer`, `dc-cc stock transfer`, `cc-cc stock transfer` | `cc-dc stock transfer` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: fromLocation, toLocation. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `sourceLocationId` | uuid (comma-separated) | No | Source location id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `sourceLocation` | string | No | Source location name. Partial, case-insensitive | `abc` |
| `destinationLocationId` | uuid (comma-separated) | No | Destination location id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `destinationLocation` | string | No | Destination location name. Partial, case-insensitive | `abc` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |

**Search fields** (`search`): document number (`challanNo`), creator name, document status, company name, GRN number, `vehicleNo`, `driverName`, `receiverName`, from location name, to location name, product name / code.

**Sortable in the database** (`sort`): `createdAt`, `createdDate`, `updatedAt`, `status`, `overAllStatus`, `documentNo`, `challanNo`.

**Example request**

```http
GET /tranfer-delivery-challan?startDate=2026-09-01&endDate=2026-09-15&stockTransferType=cc-dc%20stock%20transfer&sourceLocationId=4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c&page=1&limit=20
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/tranfer-delivery-challan?startDate=2026-09-01&endDate=2026-09-15&stockTransferType=cc-dc%20stock%20transfer&sourceLocationId=4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c&page=1&limit=20" \
  -H "Authorization: Bearer <token>"
```

**Response `200`**

| Field | Type | Description |
|---|---|---|
| `status` | string | `"success"` |
| `data` | object[] | The records (fields below) |
| `meta.total` | number | Total matching records |
| `meta.page` | number | Current page |
| `meta.totalPages` | number | Total pages |

```json
{
  "status": "success",
  "data": [
    {
      "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "documentId": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "overAllStatus": "hold",
      "createdBy": "string",
      "createdDate": "2026-09-15",
      "createdTime": "10:30 AM",
      "challanNo": "string",
      "stockTransferType": "string",
      "approvalStatus": "string",
      "requestingDepartment": "string",
      "companyName": "string",
      "fromLocation": "string",
      "toLocation": "string",
      "transitInsuranceNo": "string",
      "totalProductAmount": "0.00",
      "netProductWeight": "0.00",
      "netPackagingMaterialWeight": "0.00",
      "totalPackagingMaterialAmount": "0.00",
      "totalAmtInWords": "string",
      "driverName": "string",
      "licenseNo": "string",
      "contactNo": "string",
      "altContactNo": "string",
      "vehicleNo": "string",
      "receiverName": "string",
      "rmn": "string",
      "remark": "string",
      "anyAttachment": []
    }
  ],
  "meta": {
    "total": 1,
    "page": 1,
    "totalPages": 1
  }
}
```

`data[]` fields:

| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Record id of the module document |
| `documentId` | string (uuid) | Approval document id (`documents.id`), used by the `/view/:docid` endpoints |
| `overAllStatus` | string | Document workflow status (`documents.status`) |
| `createdBy` | string | Document creator's name |
| `createdDate` | string | Creation date, `YYYY-MM-DD` |
| `createdTime` | string | Creation time, `hh:mm A` |
| `challanNo` | string | Challan number |
| `stockTransferType` | string | Stock transfer type |
| `approvalStatus` | string \| null | Challan approval status |
| `requestingDepartment` | string \| null | Requesting department |
| `companyName` | string \| null | Company name |
| `fromLocation` | string \| null | Source location name |
| `toLocation` | string \| null | Destination location name |
| `transitInsuranceNo` | string \| null | Transit insurance number |
| `totalProductAmount` | string \| null | Total product amount (decimal, serialised as a string) |
| `netProductWeight` | string \| null | Net product weight (decimal, serialised as a string) |
| `netPackagingMaterialWeight` | string \| null | Net packaging material weight (decimal, serialised as a string) |
| `totalPackagingMaterialAmount` | string \| null | Total packaging material amount (decimal, serialised as a string) |
| `totalAmtInWords` | string \| null | Total amount in words |
| `driverName` | string \| null | Driver name |
| `licenseNo` | string \| null | Driver license number |
| `contactNo` | string \| null | Contact number |
| `altContactNo` | string \| null | Alternate contact number |
| `vehicleNo` | string \| null | Vehicle number |
| `receiverName` | string \| null | Receiver name |
| `rmn` | string \| null | RMN |
| `remark` | string \| null | Remark |
| `anyAttachment` | string[] \| null | Attachment URLs |

**No matching records:** `200` with `data: []` and a total of `0`.

**Errors:** `400` invalid filter · `401` not authenticated · `500` unexpected error (see [§ 5](#5-error-responses)).

#### Export Delivery Challan – Stock Transfer

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/tranfer-delivery-challan/export/excel` |
| **Purpose** | Downloads every Delivery Challan – Stock Transfer document matching the filters as an `.xlsx` workbook. Same filters, search, sort and visibility as Get All; **no pagination**. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | Same row visibility as Get All. |
| **Path parameters** | None |

**Query parameters:** every Get All parameter above; `page` and `limit` are ignored.

<details><summary>Full parameter list</summary>

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page`, `limit` | number | No | **Ignored** — the export always contains every matching record. | — |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `delivery_challan_purchase.createdAt` (no challan date column) | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `delivery_challan_purchase.createdAt` (no challan date column) | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `approvalStatus` | enum (comma-separated) | No | Challan approval status. Exact match; any of: `pending`, `approved`, `notapproved`, `active`, `incomplete`, `draft` | `pending` |
| `requestingDepartment` | enum (comma-separated) | No | Requesting department. Exact match; any of: `admin`, `hr`, `operations`, `sale`, `procurement`, `it`, `business_development`, `exports`, `branding_&_marketing`, `farming`, `quality_checking`, `other` | `admin` |
| `grnNo` | string | No | Linked GRN number. Partial, case-insensitive | `GRN2026` |
| `vehicleNo` | string | No | Vehicle number. Partial, case-insensitive | `MH12AB1234` |
| `driverName` | string | No | Driver name. Partial, case-insensitive | `Ramesh` |
| `isReturned` | boolean | No | Challan has been returned. `true` / `false` (also `1`/`0`, `yes`/`no`) | `true` |
| `minAmount` | number | No | Total product amount >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Total product amount <= value. Inclusive upper bound | `50000` |
| `stockTransferType` | enum (comma-separated) | No | Stock transfer type. Exact match; any of: `cc-dc stock transfer`, `dc-dc stock transfer`, `dc-cc stock transfer`, `cc-cc stock transfer` | `cc-dc stock transfer` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: fromLocation, toLocation. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `sourceLocationId` | uuid (comma-separated) | No | Source location id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `sourceLocation` | string | No | Source location name. Partial, case-insensitive | `abc` |
| `destinationLocationId` | uuid (comma-separated) | No | Destination location id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `destinationLocation` | string | No | Destination location name. Partial, case-insensitive | `abc` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |

</details>

**Example request**

```http
GET /tranfer-delivery-challan/export/excel?startDate=2026-09-01&endDate=2026-09-15&stockTransferType=cc-dc%20stock%20transfer&sourceLocationId=4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/tranfer-delivery-challan/export/excel?startDate=2026-09-01&endDate=2026-09-15&stockTransferType=cc-dc%20stock%20transfer&sourceLocationId=4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c" \
  -H "Authorization: Bearer <token>" \
  --output Stock_Transfer_Delivery_Challan.xlsx
```

**Response `200`**

```text
Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Content-Disposition: attachment; filename="Stock_Transfer_Delivery_Challan_2026-09-15.xlsx"; filename*=UTF-8''Stock_Transfer_Delivery_Challan_2026-09-15.xlsx
Cache-Control: no-store
X-Export-Record-Count: <documents in the file>
```

File name pattern: `Stock_Transfer_Delivery_Challan_<YYYY-MM-DD>.xlsx`. Sheets:

| Sheet | Columns | Column headers (in order) |
|---|---|---|
| `Stock Transfer DC` | 48 | Challan No, Challan Record ID, Stock Transfer Type, Source Location, Destination Location, Company, Company GST No, Office, GRN No, Requesting Department, Challan Approval Status, Transit Insurance No, Vehicle No, Driver Name, Driver License No, Contact No, Alternate Contact No, RMN, Receiver Name, Total Product Amount, Net Product Weight, Net Packaging Material Weight, Total Packaging Material Amount, Total Amount In Words, Returned, Attachments, Remark, Created By, Created By Employee ID, Created Date, Updated Date, Document ID, Overall Status, Last Approval Stage, Last Approval Action, Last Action By, Last Action Date, Approved By, Approval Date, Rejected By, Rejection Date, Rejection Reason, Document Remarks, Document Created By, Document Created By Employee ID, Document Created Date, Document Updated Date, Inventory Posted |
| `Stock Transfer DC Items` | 31 | Challan No, Challan Record ID, Item ID, Product ID, Product Code, Product Name, Category, Variant, Variant Code, UOM, Sale UOM, Quantity, Accepted Qty, Rejected Qty, Returned Qty, Changed Qty, Unit Price, Changed Price, Amount, Gross Weight, Packing Material Weight, Net Weight, Packing Material Quantity, Packaging Material, Packaging Material UOM, Packaging Material Quantity, Packaging Material Unit Price, Packaging Material Amount, Packaging Material Total Weight, Created Date, Updated Date |
| `Approvals` | 11 | Challan No, Record ID, Document ID, Overall Status, Stage, Stage Order, Action, Action By, Action By User ID, Reason, Action Date |

**Errors:** `400` invalid filter (JSON) · `401` not authenticated · `404 {"status":"fail","message":"No records found for the given filters"}` · `500` unexpected error.

#### Import Delivery Challan – Stock Transfer

Not available — no import endpoint exists for this module (see [§ 7](#7-import-apis)).

---

### 8.9 Other Delivery Challan

Document type `DC_TYPE_OTHER` · Record table: `delivery_challan_purchase` · Document number: `challanNo`

#### Get All Other Delivery Challan

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/other-delivery-challan` |
| **Purpose** | Lists the Other Delivery Challan documents visible to the user, filtered, searched, sorted and paginated. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | No role check. Visible to the document creator, or a user in the **level-1 or level-2 approver** block (any status). |
| **Implemented in** | `OtherDeliveryChallanController (GET /) → OtherDeliveryChallanService.getAll` |
| **Path parameters** | None |

**Query parameters** (all optional)

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page` | number | No | Page number. When absent, page 1 and limit 10 are used (SQL `OFFSET`/`LIMIT`); always paginated. | `1` |
| `limit` | number | No | Records per page (see `page`). | `20` |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `delivery_challan_purchase.createdAt` (no challan date column) | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `delivery_challan_purchase.createdAt` (no challan date column) | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `approvalStatus` | enum (comma-separated) | No | Challan approval status. Exact match; any of: `pending`, `approved`, `notapproved`, `active`, `incomplete`, `draft` | `pending` |
| `requestingDepartment` | enum (comma-separated) | No | Requesting department. Exact match; any of: `admin`, `hr`, `operations`, `sale`, `procurement`, `it`, `business_development`, `exports`, `branding_&_marketing`, `farming`, `quality_checking`, `other` | `admin` |
| `grnNo` | string | No | Linked GRN number. Partial, case-insensitive | `GRN2026` |
| `vehicleNo` | string | No | Vehicle number. Partial, case-insensitive | `MH12AB1234` |
| `driverName` | string | No | Driver name. Partial, case-insensitive | `Ramesh` |
| `isReturned` | boolean | No | Challan has been returned. `true` / `false` (also `1`/`0`, `yes`/`no`) | `true` |
| `minAmount` | number | No | Total product amount >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Total product amount <= value. Inclusive upper bound | `50000` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: fromLocation. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `customerName` | string | No | Party name. Partial, case-insensitive | `ABC Foods` |
| `customerContactNo` | string | No | Party contact number. Partial, case-insensitive | `abc` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |

**Search fields** (`search`): document number (`challanNo`), creator name, document status, company name, GRN number, `vehicleNo`, `driverName`, `receiverName`, `customer`, from location name, product name / code.

**Sortable in the database** (`sort`): `createdAt`, `createdDate`, `updatedAt`, `status`, `overAllStatus`, `documentNo`, `challanNo`.

**Example request**

```http
GET /other-delivery-challan?startDate=2026-09-01&endDate=2026-09-15&customerName=Traders&vehicleNo=MH12&page=1&limit=20
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/other-delivery-challan?startDate=2026-09-01&endDate=2026-09-15&customerName=Traders&vehicleNo=MH12&page=1&limit=20" \
  -H "Authorization: Bearer <token>"
```

**Response `200`**

| Field | Type | Description |
|---|---|---|
| `status` | string | `"success"` |
| `data` | object[] | The records (fields below) |
| `allRecords` | number | Total matching records |
| `totalPages` | number | Total pages |
| `page` | number | Current page |

```json
{
  "status": "success",
  "data": [
    {
      "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "documentId": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "overAllStatus": "hold",
      "createdBy": "string",
      "createdDate": "2026-09-15",
      "createdTime": "10:30 AM",
      "challanNo": "string",
      "approvalStatus": "string",
      "requestingDepartment": "string",
      "companyName": "string",
      "office": "string",
      "grnNo": "string",
      "fromLocation": "string",
      "customer": "string",
      "customerContactNo": "string",
      "customerEmail": "string",
      "customerAddress": "string",
      "transitInsuranceNo": "string",
      "totalProductAmount": "0.00",
      "netProductWeight": "0.00",
      "netPackagingMaterialWeight": "0.00",
      "totalPackagingMaterialAmount": "0.00",
      "totalAmtInWords": "string",
      "driverName": "string",
      "licenseNo": "string",
      "contactNo": "string",
      "altContactNo": "string",
      "vehicleNo": "string",
      "receiverName": "string",
      "rmn": "string",
      "remark": "string",
      "anyAttachment": []
    }
  ],
  "allRecords": 1,
  "totalPages": 1,
  "page": 1
}
```

`data[]` fields:

| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Record id of the module document |
| `documentId` | string (uuid) | Approval document id (`documents.id`), used by the `/view/:docid` endpoints |
| `overAllStatus` | string | Document workflow status (`documents.status`) |
| `createdBy` | string | Document creator's name |
| `createdDate` | string | Creation date, `YYYY-MM-DD` |
| `createdTime` | string | Creation time, `hh:mm A` |
| `challanNo` | string | Challan number |
| `approvalStatus` | string \| null | Challan approval status |
| `requestingDepartment` | string \| null | Requesting department |
| `companyName` | string \| null | Company name |
| `office` | string \| null | Office name |
| `grnNo` | string \| null | Linked GRN number |
| `fromLocation` | string \| null | From location name |
| `customer` | string \| null | Party name (free text) |
| `customerContactNo` | string \| null | Party contact number |
| `customerEmail` | string \| null | Party email |
| `customerAddress` | string \| null | Party address (formatted) |
| `transitInsuranceNo` | string \| null | Transit insurance number |
| `totalProductAmount` | string \| null | Total product amount (decimal, serialised as a string) |
| `netProductWeight` | string \| null | Net product weight (decimal, serialised as a string) |
| `netPackagingMaterialWeight` | string \| null | Net packaging material weight (decimal, serialised as a string) |
| `totalPackagingMaterialAmount` | string \| null | Total packaging material amount (decimal, serialised as a string) |
| `totalAmtInWords` | string \| null | Total amount in words |
| `driverName` | string \| null | Driver name |
| `licenseNo` | string \| null | Driver license number |
| `contactNo` | string \| null | Contact number |
| `altContactNo` | string \| null | Alternate contact number |
| `vehicleNo` | string \| null | Vehicle number |
| `receiverName` | string \| null | Receiver name |
| `rmn` | string \| null | RMN |
| `remark` | string \| null | Remark |
| `anyAttachment` | string[] \| null | Attachment URLs |

**No matching records:** `200` with `data: []` and a total of `0`.

**Errors:** `400` invalid filter · `401` not authenticated · `500` unexpected error (see [§ 5](#5-error-responses)).

#### Export Other Delivery Challan

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/other-delivery-challan/export/excel` |
| **Purpose** | Downloads every Other Delivery Challan document matching the filters as an `.xlsx` workbook. Same filters, search, sort and visibility as Get All; **no pagination**. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | Same row visibility as Get All. |
| **Path parameters** | None |

**Query parameters:** every Get All parameter above; `page` and `limit` are ignored.

<details><summary>Full parameter list</summary>

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page`, `limit` | number | No | **Ignored** — the export always contains every matching record. | — |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `delivery_challan_purchase.createdAt` (no challan date column) | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `delivery_challan_purchase.createdAt` (no challan date column) | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `approvalStatus` | enum (comma-separated) | No | Challan approval status. Exact match; any of: `pending`, `approved`, `notapproved`, `active`, `incomplete`, `draft` | `pending` |
| `requestingDepartment` | enum (comma-separated) | No | Requesting department. Exact match; any of: `admin`, `hr`, `operations`, `sale`, `procurement`, `it`, `business_development`, `exports`, `branding_&_marketing`, `farming`, `quality_checking`, `other` | `admin` |
| `grnNo` | string | No | Linked GRN number. Partial, case-insensitive | `GRN2026` |
| `vehicleNo` | string | No | Vehicle number. Partial, case-insensitive | `MH12AB1234` |
| `driverName` | string | No | Driver name. Partial, case-insensitive | `Ramesh` |
| `isReturned` | boolean | No | Challan has been returned. `true` / `false` (also `1`/`0`, `yes`/`no`) | `true` |
| `minAmount` | number | No | Total product amount >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Total product amount <= value. Inclusive upper bound | `50000` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: fromLocation. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `customerName` | string | No | Party name. Partial, case-insensitive | `ABC Foods` |
| `customerContactNo` | string | No | Party contact number. Partial, case-insensitive | `abc` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |

</details>

**Example request**

```http
GET /other-delivery-challan/export/excel?startDate=2026-09-01&endDate=2026-09-15&customerName=Traders&vehicleNo=MH12
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/other-delivery-challan/export/excel?startDate=2026-09-01&endDate=2026-09-15&customerName=Traders&vehicleNo=MH12" \
  -H "Authorization: Bearer <token>" \
  --output Other_Delivery_Challan.xlsx
```

**Response `200`**

```text
Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Content-Disposition: attachment; filename="Other_Delivery_Challan_2026-09-15.xlsx"; filename*=UTF-8''Other_Delivery_Challan_2026-09-15.xlsx
Cache-Control: no-store
X-Export-Record-Count: <documents in the file>
```

File name pattern: `Other_Delivery_Challan_<YYYY-MM-DD>.xlsx`. Sheets:

| Sheet | Columns | Column headers (in order) |
|---|---|---|
| `Other DC` | 50 | Challan No, Challan Record ID, From Location, Party Name, Party Contact No, Party Email, Party Address, Company, Company GST No, Office, GRN No, Requesting Department, Challan Approval Status, Transit Insurance No, Vehicle No, Driver Name, Driver License No, Contact No, Alternate Contact No, RMN, Receiver Name, Total Product Amount, Net Product Weight, Net Packaging Material Weight, Total Packaging Material Amount, Total Amount In Words, Returned, Attachments, Remark, Created By, Created By Employee ID, Created Date, Updated Date, Document ID, Overall Status, Last Approval Stage, Last Approval Action, Last Action By, Last Action Date, Approved By, Approval Date, Rejected By, Rejection Date, Rejection Reason, Document Remarks, Document Created By, Document Created By Employee ID, Document Created Date, Document Updated Date, Inventory Posted |
| `Other DC Items` | 31 | Challan No, Challan Record ID, Item ID, Product ID, Product Code, Product Name, Category, Variant, Variant Code, UOM, Sale UOM, Quantity, Accepted Qty, Rejected Qty, Returned Qty, Changed Qty, Unit Price, Changed Price, Amount, Gross Weight, Packing Material Weight, Net Weight, Packing Material Quantity, Packaging Material, Packaging Material UOM, Packaging Material Quantity, Packaging Material Unit Price, Packaging Material Amount, Packaging Material Total Weight, Created Date, Updated Date |
| `Approvals` | 11 | Challan No, Record ID, Document ID, Overall Status, Stage, Stage Order, Action, Action By, Action By User ID, Reason, Action Date |

**Errors:** `400` invalid filter (JSON) · `401` not authenticated · `404 {"status":"fail","message":"No records found for the given filters"}` · `500` unexpected error.

#### Import Other Delivery Challan

Not available — no import endpoint exists for this module (see [§ 7](#7-import-apis)).

---

### 8.10 Final Invoice

Document type `final-invoice` · Record table: `invoices` · Document number: `invoiceNo`

#### Get All Final Invoice

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/final-invoice` |
| **Purpose** | Lists the Final Invoice documents visible to the user, filtered, searched, sorted and paginated. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | No role check. Visible to the document creator, or a user in the **level-1 or level-2 approver** block (invoice not deleted). |
| **Implemented in** | `FinalInvoiceController (GET /) → FinalInvoiceService.getAll` |
| **Path parameters** | None |

**Query parameters** (all optional)

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page` | number | No | Page number. When absent, page 1 and limit 10 are used (SQL `OFFSET`/`LIMIT`); always paginated. | `1` |
| `limit` | number | No | Records per page (see `page`). | `20` |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `invoices.invoiceDate` | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `invoices.invoiceDate` | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `paymentStatus` | enum (comma-separated) | No | Payment status. Exact match; any of: `paid`, `unpaid` | `paid` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `customerId` | uuid (comma-separated) | No | Customer id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `customerCode` | string | No | Customer code. Partial, case-insensitive | `CUST001` |
| `customerName` | string | No | Customer name. Partial, case-insensitive | `ABC Foods` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: fromLocation. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `deliveryChallanNo` | string | No | Delivery challan number. Partial, case-insensitive | `CN2026` |
| `poNumber` | string | No | PO number. Partial, case-insensitive | `PO-778` |
| `vehicleNo` | string | No | Vehicle number. Partial, case-insensitive | `MH12AB1234` |
| `placeOfSupply` | string | No | Place of supply. Partial, case-insensitive | `abc` |
| `minAmount` | number | No | Invoice grand total >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Invoice grand total <= value. Inclusive upper bound | `50000` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |

**Search fields** (`search`): document number (`invoiceNo`), creator name, document status, company name, customer name, customer code, delivery challan number, `vehicleNo`, `poNumber`, from location name, product name / code.

**Sortable in the database** (`sort`): `createdAt`, `createdDate`, `updatedAt`, `status`, `overAllStatus`, `documentNo`, `invoiceNo`, `invoiceDate`, `totalAmount`.

**Example request**

```http
GET /final-invoice?startDate=2026-09-01&endDate=2026-09-15&paymentStatus=unpaid&customerId=7a8b9c0d-1e2f-4a3b-8c4d-5e6f7a8b9c0d&minAmount=5000&page=1&limit=20
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/final-invoice?startDate=2026-09-01&endDate=2026-09-15&paymentStatus=unpaid&customerId=7a8b9c0d-1e2f-4a3b-8c4d-5e6f7a8b9c0d&minAmount=5000&page=1&limit=20" \
  -H "Authorization: Bearer <token>"
```

**Response `200`**

| Field | Type | Description |
|---|---|---|
| `status` | string | `"success"` |
| `data` | object[] | The records (fields below) |
| `allRecords` | number | Total matching records |
| `totalPages` | number | Total pages |
| `page` | number | Current page |

```json
{
  "status": "success",
  "data": [
    {
      "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "documentId": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "overAllStatus": "hold",
      "createdBy": "string",
      "createdDate": "2026-09-15",
      "createdTime": "10:30 AM",
      "invoiceNo": "string",
      "invoiceDate": "string",
      "ammountStatus": "string",
      "companyName": "string",
      "customerName": "string",
      "deliveryChallan": "string",
      "poNumber": "string",
      "vehicleNo": "string",
      "fromLocation": "string",
      "billingAddress": "string",
      "deliveryAddress": "string",
      "totalProductAmount": "0.00",
      "netProductWeight": "0.00",
      "grossProductWeight": 0,
      "totalAmount": "0.00"
    }
  ],
  "allRecords": 1,
  "totalPages": 1,
  "page": 1
}
```

`data[]` fields:

| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Record id of the module document |
| `documentId` | string (uuid) | Approval document id (`documents.id`), used by the `/view/:docid` endpoints |
| `overAllStatus` | string | Document workflow status (`documents.status`) |
| `createdBy` | string | Document creator's name |
| `createdDate` | string | Creation date, `YYYY-MM-DD` |
| `createdTime` | string | Creation time, `hh:mm A` |
| `invoiceNo` | string | Invoice number |
| `invoiceDate` | string \| null | Invoice date |
| `ammountStatus` | string \| null | Payment status: paid or unpaid |
| `companyName` | string \| null | Company name |
| `customerName` | string \| null | Customer name |
| `deliveryChallan` | string \| null | Linked delivery challan number |
| `poNumber` | string \| null | PO number |
| `vehicleNo` | string \| null | Vehicle number |
| `fromLocation` | string \| null | From location name |
| `billingAddress` | string \| null | Billing address (formatted) |
| `deliveryAddress` | string \| null | Delivery address (formatted) |
| `totalProductAmount` | string \| null | Total product amount (decimal, serialised as a string) |
| `netProductWeight` | string \| null | Net product weight (decimal, serialised as a string) |
| `grossProductWeight` | number | Sum of line gross weights |
| `totalAmount` | string \| null | Grand total (decimal, serialised as a string) |

**No matching records:** `404 {"status":"fail","message":"No final invoices found"}`.

**Errors:** `400` invalid filter · `401` not authenticated · `500` unexpected error (see [§ 5](#5-error-responses)).

#### Export Final Invoice

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/final-invoice/export/excel` |
| **Purpose** | Downloads every Final Invoice document matching the filters as an `.xlsx` workbook. Same filters, search, sort and visibility as Get All; **no pagination**. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | Same row visibility as Get All. |
| **Path parameters** | None |

**Query parameters:** every Get All parameter above; `page` and `limit` are ignored.

<details><summary>Full parameter list</summary>

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page`, `limit` | number | No | **Ignored** — the export always contains every matching record. | — |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `invoices.invoiceDate` | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `invoices.invoiceDate` | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `paymentStatus` | enum (comma-separated) | No | Payment status. Exact match; any of: `paid`, `unpaid` | `paid` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `customerId` | uuid (comma-separated) | No | Customer id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `customerCode` | string | No | Customer code. Partial, case-insensitive | `CUST001` |
| `customerName` | string | No | Customer name. Partial, case-insensitive | `ABC Foods` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: fromLocation. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `deliveryChallanNo` | string | No | Delivery challan number. Partial, case-insensitive | `CN2026` |
| `poNumber` | string | No | PO number. Partial, case-insensitive | `PO-778` |
| `vehicleNo` | string | No | Vehicle number. Partial, case-insensitive | `MH12AB1234` |
| `placeOfSupply` | string | No | Place of supply. Partial, case-insensitive | `abc` |
| `minAmount` | number | No | Invoice grand total >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Invoice grand total <= value. Inclusive upper bound | `50000` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |

</details>

**Example request**

```http
GET /final-invoice/export/excel?startDate=2026-09-01&endDate=2026-09-15&paymentStatus=unpaid&customerId=7a8b9c0d-1e2f-4a3b-8c4d-5e6f7a8b9c0d&minAmount=5000
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/final-invoice/export/excel?startDate=2026-09-01&endDate=2026-09-15&paymentStatus=unpaid&customerId=7a8b9c0d-1e2f-4a3b-8c4d-5e6f7a8b9c0d&minAmount=5000" \
  -H "Authorization: Bearer <token>" \
  --output Final_Invoice.xlsx
```

**Response `200`**

```text
Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Content-Disposition: attachment; filename="Final_Invoice_2026-09-15.xlsx"; filename*=UTF-8''Final_Invoice_2026-09-15.xlsx
Cache-Control: no-store
X-Export-Record-Count: <documents in the file>
```

File name pattern: `Final_Invoice_<YYYY-MM-DD>.xlsx`. Sheets:

| Sheet | Columns | Column headers (in order) |
|---|---|---|
| `Final Invoice` | 49 | Invoice No, Invoice Record ID, Invoice Date, Company, Company GST No, Customer ID, Customer Name, Customer Code, Customer Contact No, Customer Email, Delivery Challan No, PO Number, From Location, Billing Address, Shipping Address, Place Of Supply, Vehicle No, Net Product Weight, Total Product Amount, Discount, Freight, Other Charges, CGST Amount, SGST Amount, IGST Amount, Total Tax Amount, Grand Total, Total Amount In Words, Payment Status, Created By, Created By Employee ID, Created Date, Updated Date, Document ID, Overall Status, Last Approval Stage, Last Approval Action, Last Action By, Last Action Date, Approved By, Approval Date, Rejected By, Rejection Date, Rejection Reason, Document Remarks, Document Created By, Document Created By Employee ID, Document Created Date, Document Updated Date |
| `Invoice Items` | 22 | Invoice No, Invoice Record ID, Item ID, Product ID, Product Code, Product Name, Category, Variant, Variant Code, HSN Code, Description, Sale UOM, Quantity, Accepted Qty, Rejected Qty, Returned Qty, Unit Price, Amount, Gross Weight, Net Weight, Created Date, Updated Date |
| `Approvals` | 11 | Invoice No, Record ID, Document ID, Overall Status, Stage, Stage Order, Action, Action By, Action By User ID, Reason, Action Date |

**Errors:** `400` invalid filter (JSON) · `401` not authenticated · `404 {"status":"fail","message":"No records found for the given filters"}` · `500` unexpected error.

#### Import Final Invoice

Not available — no import endpoint exists for this module (see [§ 7](#7-import-apis)).

---

### 8.11 Return by Customer

Document type `return-by-customer` · Record table: `return_by_customer` · Document number: `rbcNo`

#### Get All Return by Customer

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/returns` |
| **Purpose** | Lists the Return by Customer documents visible to the user, filtered, searched, sorted and paginated. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | No role check. Visible to the document creator, or a user in the **level-1 or level-2 approver** block (any status). |
| **Implemented in** | `PostReturnByCustomerController (GET /) → PostReturnByCustomerService.getAllPostReturnByCustomer` |
| **Path parameters** | None |

**Query parameters** (all optional)

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page` | number | No | Page number. When absent, page 1 and limit 10 are used (SQL `OFFSET`/`LIMIT`); always paginated. | `1` |
| `limit` | number | No | Records per page (see `page`). | `20` |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `return_by_customer.date` | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `return_by_customer.date` | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `customerId` | uuid (comma-separated) | No | Customer id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `customerCode` | string | No | Customer code. Partial, case-insensitive | `CUST001` |
| `customerName` | string | No | Customer name. Partial, case-insensitive | `ABC Foods` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: location. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `deliveryChallanNo` | string | No | Original delivery challan number. Partial, case-insensitive | `CN2026` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |

**Search fields** (`search`): document number (`rbcNo`), creator name, document status, delivery challan number, company name, customer name, customer code, location name, `remark`, product name / code.

**Sortable in the database** (`sort`): `createdAt`, `createdDate`, `updatedAt`, `status`, `overAllStatus`, `documentNo`, `rbcNo`, `date`.

**Example request**

```http
GET /returns?startDate=2026-09-01&endDate=2026-09-15&customerId=7a8b9c0d-1e2f-4a3b-8c4d-5e6f7a8b9c0d&deliveryChallanNo=CN2026&page=1&limit=20
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/returns?startDate=2026-09-01&endDate=2026-09-15&customerId=7a8b9c0d-1e2f-4a3b-8c4d-5e6f7a8b9c0d&deliveryChallanNo=CN2026&page=1&limit=20" \
  -H "Authorization: Bearer <token>"
```

**Response `200`**

| Field | Type | Description |
|---|---|---|
| `status` | string | `"success"` |
| `data` | object[] | The records (fields below) |
| `allRecords` | number | Total matching records |
| `totalPages` | number | Total pages |
| `page` | number | Current page |

```json
{
  "status": "success",
  "data": [
    {
      "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "documentId": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "overAllStatus": "hold",
      "createdBy": "string",
      "createdDate": "2026-09-15",
      "createdTime": "10:30 AM",
      "rbcNo": "string",
      "date": "string",
      "companyName": "string",
      "deliveryChallanNo": "string",
      "remark": "string",
      "returnedProducts": [
        {
          "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
          "productName": "string",
          "saleUoM": "string",
          "unitPrice": "0.00"
        }
      ]
    }
  ],
  "allRecords": 1,
  "totalPages": 1,
  "page": 1
}
```

`data[]` fields:

| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Record id of the module document |
| `documentId` | string (uuid) | Approval document id (`documents.id`), used by the `/view/:docid` endpoints |
| `overAllStatus` | string | Document workflow status (`documents.status`) |
| `createdBy` | string | Document creator's name |
| `createdDate` | string | Creation date, `YYYY-MM-DD` |
| `createdTime` | string | Creation time, `hh:mm A` |
| `rbcNo` | string | Return number |
| `date` | string \| null | Return date |
| `companyName` | string \| null | Company name |
| `deliveryChallanNo` | string \| null | Original delivery challan number |
| `remark` | string \| null | Remark |
| `returnedProducts` | object[] | Returned lines (see below) |

`returnedProducts` fields:

| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Line id |
| `productName` | string \| null | Product name |
| `saleUoM` | string \| null | Sale UOM |
| `unitPrice` | string | Unit price (decimal, serialised as a string) |

**No matching records:** `200` with `data: []` and a total of `0`.

**Errors:** `400` invalid filter · `401` not authenticated · `500` unexpected error (see [§ 5](#5-error-responses)).

#### Export Return by Customer

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/returns/export/excel` |
| **Purpose** | Downloads every Return by Customer document matching the filters as an `.xlsx` workbook. Same filters, search, sort and visibility as Get All; **no pagination**. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | Same row visibility as Get All. |
| **Path parameters** | None |

**Query parameters:** every Get All parameter above; `page` and `limit` are ignored.

<details><summary>Full parameter list</summary>

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page`, `limit` | number | No | **Ignored** — the export always contains every matching record. | — |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `return_by_customer.date` | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `return_by_customer.date` | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `customerId` | uuid (comma-separated) | No | Customer id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `customerCode` | string | No | Customer code. Partial, case-insensitive | `CUST001` |
| `customerName` | string | No | Customer name. Partial, case-insensitive | `ABC Foods` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: location. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `deliveryChallanNo` | string | No | Original delivery challan number. Partial, case-insensitive | `CN2026` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |

</details>

**Example request**

```http
GET /returns/export/excel?startDate=2026-09-01&endDate=2026-09-15&customerId=7a8b9c0d-1e2f-4a3b-8c4d-5e6f7a8b9c0d&deliveryChallanNo=CN2026
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/returns/export/excel?startDate=2026-09-01&endDate=2026-09-15&customerId=7a8b9c0d-1e2f-4a3b-8c4d-5e6f7a8b9c0d&deliveryChallanNo=CN2026" \
  -H "Authorization: Bearer <token>" \
  --output Return_By_Customer.xlsx
```

**Response `200`**

```text
Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Content-Disposition: attachment; filename="Return_By_Customer_2026-09-15.xlsx"; filename*=UTF-8''Return_By_Customer_2026-09-15.xlsx
Cache-Control: no-store
X-Export-Record-Count: <documents in the file>
```

File name pattern: `Return_By_Customer_<YYYY-MM-DD>.xlsx`. Sheets:

| Sheet | Columns | Column headers (in order) |
|---|---|---|
| `Return By Customer` | 36 | Return No, Return Record ID, Return Date, Company, Company GST No, Location, Customer ID, Customer Name, Customer Code, Customer Contact No, Customer Email, Original Delivery Challan No, Line Items, Returned Amount, Rejected Amount, Remark, Created By, Created By Employee ID, Created Date, Updated Date, Document ID, Overall Status, Last Approval Stage, Last Approval Action, Last Action By, Last Action Date, Approved By, Approval Date, Rejected By, Rejection Date, Rejection Reason, Document Remarks, Document Created By, Document Created By Employee ID, Document Created Date, Document Updated Date |
| `Returned Items` | 27 | Return No, Return Record ID, Original Delivery Challan No, Item ID, Product ID, Product Code, Product Name, Category, Variant, Variant Code, Sale UOM, Original DC Quantity, DC Accepted Qty, Unit Price, Returned Qty, Returned Amount, Returned Gross Weight, Returned Packing Material Weight, Returned Net Weight, Rejected Qty, Rejected Amount, Rejected Gross Weight, Rejected Packing Material Weight, Rejected Net Weight, Changed, Created Date, Updated Date |
| `Approvals` | 11 | Return No, Record ID, Document ID, Overall Status, Stage, Stage Order, Action, Action By, Action By User ID, Reason, Action Date |

**Errors:** `400` invalid filter (JSON) · `401` not authenticated · `404 {"status":"fail","message":"No records found for the given filters"}` · `500` unexpected error.

#### Import Return by Customer

Not available — no import endpoint exists for this module (see [§ 7](#7-import-apis)).

---

### 8.12 Return to Vendor

Document type `return-to-vendor` · Record table: `return_to_vendor` · Document number: `rtvNo`

#### Get All Return to Vendor

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/return-to-vendor` |
| **Purpose** | Lists the Return to Vendor documents visible to the user, filtered, searched, sorted and paginated. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | No role check. Visible to the document creator, or a user in the **level-1 or level-2 approver** block (any status). |
| **Implemented in** | `ReturnToVendorController (GET /) → ReturnToVendorService.getAll` |
| **Path parameters** | None |

**Query parameters** (all optional)

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page` | number | No | Page number. `page` defaults to 1 and `limit` to 10 in the controller; always paginated. | `1` |
| `limit` | number | No | Records per page (see `page`). | `20` |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `return_to_vendor.returnDate` | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `return_to_vendor.returnDate` | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `vendorId` | uuid (comma-separated) | No | Vendor id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `vendorCode` | string | No | Vendor code. Partial, case-insensitive | `VEN001` |
| `vendorName` | string | No | Vendor name. Partial, case-insensitive | `Agro Traders` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: location. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `grnNo` | string | No | Original GRN number. Partial, case-insensitive | `GRN2026` |
| `minAmount` | number | No | Return total amount >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Return total amount <= value. Inclusive upper bound | `50000` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |
| `returnReason` | string | No | Return reason on any line (partial) | `damaged` |

**Search fields** (`search`): document number (`rtvNo`), creator name, document status, GRN number, company name, vendor name, vendor code, location name, `remark`, product name / code.

**Sortable in the database** (`sort`): `createdAt`, `createdDate`, `updatedAt`, `status`, `overAllStatus`, `documentNo`, `rtvNo`, `returnDate`, `totalAmt`.

**Example request**

```http
GET /return-to-vendor?startDate=2026-09-01&endDate=2026-09-15&vendorId=9b2d4c1e-5f6a-4b7c-8d9e-0a1b2c3d4e5f&returnReason=damaged&page=1&limit=20
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/return-to-vendor?startDate=2026-09-01&endDate=2026-09-15&vendorId=9b2d4c1e-5f6a-4b7c-8d9e-0a1b2c3d4e5f&returnReason=damaged&page=1&limit=20" \
  -H "Authorization: Bearer <token>"
```

**Response `200`**

| Field | Type | Description |
|---|---|---|
| `status` | string | `"success"` |
| `data` | object[] | The records (fields below) |
| `totalRecords` | number | Total matching records |
| `totalPages` | number | Total pages |
| `page` | number | Current page |
| `message` | string | `"Return to vendor records fetched successfully"` |

```json
{
  "status": "success",
  "data": [
    {
      "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "documentId": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "overAllStatus": "hold",
      "createdBy": "string",
      "createdDate": "2026-09-15",
      "createdTime": "10:30 AM",
      "rtvNo": "string",
      "returnDate": "string",
      "grnNo": "string",
      "companyName": "string",
      "location": "string",
      "selectedVendor": "string",
      "returnedGrossWeight": "0.00",
      "returnedNetWeight": "0.00",
      "totalAmt": "0.00",
      "amtWords": "string",
      "remark": "string"
    }
  ],
  "totalRecords": 1,
  "totalPages": 1,
  "page": 1,
  "message": "Return to vendor records fetched successfully"
}
```

`data[]` fields:

| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Record id of the module document |
| `documentId` | string (uuid) | Approval document id (`documents.id`), used by the `/view/:docid` endpoints |
| `overAllStatus` | string | Document workflow status (`documents.status`) |
| `createdBy` | string | Document creator's name |
| `createdDate` | string | Creation date, `YYYY-MM-DD` |
| `createdTime` | string | Creation time, `hh:mm A` |
| `rtvNo` | string | Return number |
| `returnDate` | string \| null | Return date |
| `grnNo` | string \| null | Original GRN number |
| `companyName` | string \| null | Company name |
| `location` | string \| null | Location name |
| `selectedVendor` | string \| null | Vendor name |
| `returnedGrossWeight` | string \| null | Returned gross weight (decimal, serialised as a string) |
| `returnedNetWeight` | string \| null | Returned net weight (decimal, serialised as a string) |
| `totalAmt` | string \| null | Total amount (decimal, serialised as a string) |
| `amtWords` | string \| null | Amount in words |
| `remark` | string \| null | Remark |

**No matching records:** `200` with `data: []` and a total of `0`.

**Errors:** `400` invalid filter · `401` not authenticated · `500` unexpected error (see [§ 5](#5-error-responses)).

#### Export Return to Vendor

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/return-to-vendor/export/excel` |
| **Purpose** | Downloads every Return to Vendor document matching the filters as an `.xlsx` workbook. Same filters, search, sort and visibility as Get All; **no pagination**. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | Same row visibility as Get All. |
| **Path parameters** | None |

**Query parameters:** every Get All parameter above; `page` and `limit` are ignored.

<details><summary>Full parameter list</summary>

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page`, `limit` | number | No | **Ignored** — the export always contains every matching record. | — |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `return_to_vendor.returnDate` | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `return_to_vendor.returnDate` | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `vendorId` | uuid (comma-separated) | No | Vendor id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `vendorCode` | string | No | Vendor code. Partial, case-insensitive | `VEN001` |
| `vendorName` | string | No | Vendor name. Partial, case-insensitive | `Agro Traders` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: location. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `grnNo` | string | No | Original GRN number. Partial, case-insensitive | `GRN2026` |
| `minAmount` | number | No | Return total amount >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Return total amount <= value. Inclusive upper bound | `50000` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |
| `returnReason` | string | No | Return reason on any line (partial) | `damaged` |

</details>

**Example request**

```http
GET /return-to-vendor/export/excel?startDate=2026-09-01&endDate=2026-09-15&vendorId=9b2d4c1e-5f6a-4b7c-8d9e-0a1b2c3d4e5f&returnReason=damaged
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/return-to-vendor/export/excel?startDate=2026-09-01&endDate=2026-09-15&vendorId=9b2d4c1e-5f6a-4b7c-8d9e-0a1b2c3d4e5f&returnReason=damaged" \
  -H "Authorization: Bearer <token>" \
  --output Return_To_Vendor.xlsx
```

**Response `200`**

```text
Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Content-Disposition: attachment; filename="Return_To_Vendor_2026-09-15.xlsx"; filename*=UTF-8''Return_To_Vendor_2026-09-15.xlsx
Cache-Control: no-store
X-Export-Record-Count: <documents in the file>
```

File name pattern: `Return_To_Vendor_<YYYY-MM-DD>.xlsx`. Sheets:

| Sheet | Columns | Column headers (in order) |
|---|---|---|
| `Return To Vendor` | 39 | Return No, Return Record ID, Return Date, Company, Company GST No, Location, Vendor ID, Vendor Name, Vendor Code, Vendor GSTN, Vendor Contact No, Original GRN No, Original Bill No, Returned Gross Weight, Returned Net Weight, Total Amount, Amount In Words, Remark, Created By, Created By Employee ID, Created Date, Updated Date, Document ID, Overall Status, Last Approval Stage, Last Approval Action, Last Action By, Last Action Date, Approved By, Approval Date, Rejected By, Rejection Date, Rejection Reason, Document Remarks, Document Created By, Document Created By Employee ID, Document Created Date, Document Updated Date, Inventory Posted |
| `RTV Items` | 24 | Return No, Return Record ID, Item ID, Product ID, Product Code, Product Name, Category, Variant, Variant Code, UOM, Return Quantity, Unit Price, Amount, Gross Weight, Packing Material Weight, Net Weight, Return Reason, RTV Line, Purchase Date, Dispatch Date, Delivery Date, Delivery Location, Created Date, Updated Date |
| `Approvals` | 11 | Return No, Record ID, Document ID, Overall Status, Stage, Stage Order, Action, Action By, Action By User ID, Reason, Action Date |

**Errors:** `400` invalid filter (JSON) · `401` not authenticated · `404 {"status":"fail","message":"No records found for the given filters"}` · `500` unexpected error.

#### Import Return to Vendor

Not available — no import endpoint exists for this module (see [§ 7](#7-import-apis)).

---

### 8.13 Second Sale

Document type `second-sale` · Record table: `second_sale_document` · Document number: `secondSaleNo`

#### Get All Second Sale

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/secondSales` |
| **Purpose** | Lists the Second Sale documents visible to the user, filtered, searched, sorted and paginated. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | No role check. Visible to the document creator, or a user in the **level-1 or level-2 approver** block (any status). |
| **Implemented in** | `SecondSaleController (GET /) → SecondSaleService.getAllSecondSales` |
| **Path parameters** | None |

**Query parameters** (all optional)

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page` | number | No | Page number. When absent, page 1 and limit 10 are used (SQL `OFFSET`/`LIMIT`); always paginated. | `1` |
| `limit` | number | No | Records per page (see `page`). | `20` |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `second_sale_document.saleDate` | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `second_sale_document.saleDate` | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: location. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `customerName` | string | No | Customer name. Partial, case-insensitive | `ABC Foods` |
| `customerContactNo` | string | No | Customer contact number. Partial, case-insensitive | `abc` |
| `deliveryChallanNo` | string | No | Source delivery challan number. Partial, case-insensitive | `CN2026` |
| `paymentMode` | string (comma-separated) | No | Payment mode (exact, case-insensitive). Exact, case-insensitive; any of the values | `cash` |
| `reasonForSale` | string | No | Reason for sale. Partial, case-insensitive | `abc` |
| `minAmount` | number | No | Second sale total amount >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Second sale total amount <= value. Inclusive upper bound | `50000` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |

**Search fields** (`search`): document number (`secondSaleNo`), creator name, document status, `customerName`, `customerContactNo`, delivery challan number, company name, location name, `reasonForSale`, product name / code.

**Sortable in the database** (`sort`): `createdAt`, `createdDate`, `updatedAt`, `status`, `overAllStatus`, `documentNo`, `secondSaleNo`, `saleDate`, `totalAmt`.

**Example request**

```http
GET /secondSales?startDate=2026-09-01&endDate=2026-09-15&paymentMode=cash&locationId=4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c&page=1&limit=20
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/secondSales?startDate=2026-09-01&endDate=2026-09-15&paymentMode=cash&locationId=4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c&page=1&limit=20" \
  -H "Authorization: Bearer <token>"
```

**Response `200`**

| Field | Type | Description |
|---|---|---|
| `status` | string | `"success"` |
| `data` | object[] | The records (fields below) |
| `allRecords` | number | Total matching records |
| `totalPages` | number | Total pages |
| `page` | number | Current page |

```json
{
  "status": "success",
  "data": [
    {
      "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "documentId": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "overAllStatus": "hold",
      "createdBy": "string",
      "createdDate": "2026-09-15",
      "createdTime": "10:30 AM",
      "secondSaleNo": "string",
      "saleDate": "string",
      "companyName": "string",
      "location": "string",
      "customerName": "string",
      "customerContactNo": "string",
      "customerEmail": "string",
      "reasonForSale": "string",
      "totalNetWeight": "0.00",
      "totalGrossWeight": "0.00",
      "totalAmt": "0.00",
      "totalAmtInWords": "string",
      "paidAmount": "0.00",
      "pendingAmt": "0.00",
      "paymentMode": "string",
      "remarks": "string"
    }
  ],
  "allRecords": 1,
  "totalPages": 1,
  "page": 1
}
```

`data[]` fields:

| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Record id of the module document |
| `documentId` | string (uuid) | Approval document id (`documents.id`), used by the `/view/:docid` endpoints |
| `overAllStatus` | string | Document workflow status (`documents.status`) |
| `createdBy` | string | Document creator's name |
| `createdDate` | string | Creation date, `YYYY-MM-DD` |
| `createdTime` | string | Creation time, `hh:mm A` |
| `secondSaleNo` | string | Second sale number |
| `saleDate` | string \| null | Sale date |
| `companyName` | string \| null | Company name |
| `location` | string \| null | Location name |
| `customerName` | string \| null | Customer name (free text) |
| `customerContactNo` | string \| null | Customer contact number |
| `customerEmail` | string \| null | Customer email |
| `reasonForSale` | string \| null | Reason for sale |
| `totalNetWeight` | string \| null | Total net weight (decimal, serialised as a string) |
| `totalGrossWeight` | string \| null | Total gross weight (decimal, serialised as a string) |
| `totalAmt` | string \| null | Total amount (decimal, serialised as a string) |
| `totalAmtInWords` | string \| null | Total amount in words |
| `paidAmount` | string \| null | Paid amount (decimal, serialised as a string) |
| `pendingAmt` | string \| null | Pending amount (decimal, serialised as a string) |
| `paymentMode` | string \| null | Payment mode |
| `remarks` | string \| null | Remarks |

**No matching records:** `200` with `data: []` and a total of `0`.

**Errors:** `400` invalid filter · `401` not authenticated · `500` unexpected error (see [§ 5](#5-error-responses)).

#### Export Second Sale

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/secondSales/export/excel` |
| **Purpose** | Downloads every Second Sale document matching the filters as an `.xlsx` workbook. Same filters, search, sort and visibility as Get All; **no pagination**. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | Same row visibility as Get All. |
| **Path parameters** | None |

**Query parameters:** every Get All parameter above; `page` and `limit` are ignored.

<details><summary>Full parameter list</summary>

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page`, `limit` | number | No | **Ignored** — the export always contains every matching record. | — |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `second_sale_document.saleDate` | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `second_sale_document.saleDate` | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: location. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `customerName` | string | No | Customer name. Partial, case-insensitive | `ABC Foods` |
| `customerContactNo` | string | No | Customer contact number. Partial, case-insensitive | `abc` |
| `deliveryChallanNo` | string | No | Source delivery challan number. Partial, case-insensitive | `CN2026` |
| `paymentMode` | string (comma-separated) | No | Payment mode (exact, case-insensitive). Exact, case-insensitive; any of the values | `cash` |
| `reasonForSale` | string | No | Reason for sale. Partial, case-insensitive | `abc` |
| `minAmount` | number | No | Second sale total amount >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Second sale total amount <= value. Inclusive upper bound | `50000` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |

</details>

**Example request**

```http
GET /secondSales/export/excel?startDate=2026-09-01&endDate=2026-09-15&paymentMode=cash&locationId=4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/secondSales/export/excel?startDate=2026-09-01&endDate=2026-09-15&paymentMode=cash&locationId=4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c" \
  -H "Authorization: Bearer <token>" \
  --output Second_Sale.xlsx
```

**Response `200`**

```text
Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Content-Disposition: attachment; filename="Second_Sale_2026-09-15.xlsx"; filename*=UTF-8''Second_Sale_2026-09-15.xlsx
Cache-Control: no-store
X-Export-Record-Count: <documents in the file>
```

File name pattern: `Second_Sale_<YYYY-MM-DD>.xlsx`. Sheets:

| Sheet | Columns | Column headers (in order) |
|---|---|---|
| `Second Sale` | 38 | Second Sale No, Second Sale Record ID, Sale Date, Company, Company GST No, Location, Source Delivery Challan No, Customer Name, Customer Contact No, Customer Email, Customer Address, Reason For Sale, Total Net Weight, Total Gross Weight, Total Amount, Total Amount In Words, Paid Amount, Pending Amount, Payment Mode, Remarks, Created Date, Updated Date, Document ID, Overall Status, Last Approval Stage, Last Approval Action, Last Action By, Last Action Date, Approved By, Approval Date, Rejected By, Rejection Date, Rejection Reason, Document Remarks, Document Created By, Document Created By Employee ID, Document Created Date, Document Updated Date |
| `Second Sale Items` | 24 | Second Sale No, Second Sale Record ID, Item ID, Product ID, Product Code, Product Name, Category, Variant, Variant Code, Sale UOM, Quantity, Unit Price, Amount, Gross Weight, Packaging Material Weight, Net Weight, Packaging Material, Packaging Material UOM, Packaging Material Quantity, Packaging Material Unit Price, Packaging Material Amount, Packaging Material Total Weight, Created Date, Updated Date |
| `Approvals` | 11 | Second Sale No, Record ID, Document ID, Overall Status, Stage, Stage Order, Action, Action By, Action By User ID, Reason, Action Date |

**Errors:** `400` invalid filter (JSON) · `401` not authenticated · `404 {"status":"fail","message":"No records found for the given filters"}` · `500` unexpected error.

#### Import Second Sale

Not available — no import endpoint exists for this module (see [§ 7](#7-import-apis)).

---

### 8.14 Vehicle Dispatch

Document type `vehicle-dispatch-register` · Record table: `dispatch` · Document number: `vehicleDispatchNo`

#### Get All Vehicle Dispatch

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/vehicleDispatches` |
| **Purpose** | Lists the Vehicle Dispatch documents visible to the user, filtered, searched, sorted and paginated. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | No role check. Visible to the document creator, or a user in the approval flow’s **level-1 approver** block (any status). |
| **Implemented in** | `VehicleDispatchController (GET /) → VehicleDispatchService.getAllvehicalDispatch` |
| **Path parameters** | None |

**Query parameters** (all optional)

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page` | number | No | Page number. Not applied (pre-existing behaviour): every filtered row is returned. `page` is echoed and `totalPages` is computed with `limit` (default 10). | `1` |
| `limit` | number | No | Records per page (see `page`). | `20` |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `dispatch.date` | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `dispatch.date` | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `vehicleType` | string (comma-separated) | No | Vehicle type (exact, case-insensitive). Exact, case-insensitive; any of the values | `abc` |
| `vehicleNo` | string | No | Vehicle number. Partial, case-insensitive | `MH12AB1234` |
| `driverName` | string | No | Driver name. Partial, case-insensitive | `Ramesh` |
| `customerName` | string | No | Client name. Partial, case-insensitive | `ABC Foods` |
| `deliveryChallanNo` | string | No | Delivery challan number. Partial, case-insensitive | `CN2026` |
| `clientGRNNo` | string | No | Client GRN number. Partial, case-insensitive | `abc` |
| `minAmount` | number | No | Transportation bill amount >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Transportation bill amount <= value. Inclusive upper bound | `50000` |

**Search fields** (`search`): document number (`vehicleDispatchNo`), creator name, document status, `vehicleNo`, `vehicleType`, `driverName`, `clientName`, delivery challan number, company name.

**Sortable in the database** (`sort`): `createdAt`, `createdDate`, `updatedAt`, `status`, `overAllStatus`, `documentNo`, `vehicleDispatchNo`, `date`.

**Example request**

```http
GET /vehicleDispatches?startDate=2026-09-01&endDate=2026-09-15&vehicleNo=MH12&driverName=Ramesh
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/vehicleDispatches?startDate=2026-09-01&endDate=2026-09-15&vehicleNo=MH12&driverName=Ramesh" \
  -H "Authorization: Bearer <token>"
```

**Response `200`**

| Field | Type | Description |
|---|---|---|
| `status` | string | `"success"` |
| `data` | object[] | The records (fields below) |
| `allRecords` | number | Total matching records |
| `totalPages` | number | Total pages |
| `page` | number | Current page |

```json
{
  "status": "success",
  "data": [
    {
      "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "documentId": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "overAllStatus": "hold",
      "createdBy": "string",
      "createdDate": "2026-09-15",
      "createdTime": "10:30 AM",
      "date": "string",
      "vehicleType": "string",
      "vehicleNo": "string",
      "driverName": "string",
      "driverMobNo": "string",
      "reachingTime": "string",
      "outTime": "string",
      "clientName": "string",
      "clientAddress": {},
      "receivingPerson": "string",
      "supervisorName": "string",
      "companyName": "string",
      "deliveryChallanNo": "string",
      "clientGRNNo": "string",
      "paymentDiscussed": 0,
      "transportationBillAmt": 0,
      "advancePaid": 0,
      "paymentTerms": "string",
      "accDeptVerification": "string",
      "netInwardQty": 0,
      "rejection": "string",
      "shrinkageDump": "string",
      "remarksPFL": "string",
      "feedbackbyTransporterOwner": "string"
    }
  ],
  "allRecords": 1,
  "totalPages": 1,
  "page": 1
}
```

`data[]` fields:

| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Record id of the module document |
| `documentId` | string (uuid) | Approval document id (`documents.id`), used by the `/view/:docid` endpoints |
| `overAllStatus` | string | Document workflow status (`documents.status`) |
| `createdBy` | string | Document creator's name |
| `createdDate` | string | Creation date, `YYYY-MM-DD` |
| `createdTime` | string | Creation time, `hh:mm A` |
| `date` | string \| null | Dispatch date |
| `vehicleType` | string \| null | Vehicle type |
| `vehicleNo` | string \| null | Vehicle number |
| `driverName` | string \| null | Driver name |
| `driverMobNo` | string \| null | Driver mobile number |
| `reachingTime` | string \| null | Reaching time |
| `outTime` | string \| null | Out time |
| `clientName` | string \| null | Client name |
| `clientAddress` | object \| null | Client address entity (includes its audit fields) |
| `receivingPerson` | string \| null | Receiving person |
| `supervisorName` | string \| null | Supervisor name |
| `companyName` | string \| null | Company name |
| `deliveryChallanNo` | string \| null | Linked delivery challan number |
| `clientGRNNo` | string \| null | Client GRN number |
| `paymentDiscussed` | number \| null | Payment discussed |
| `transportationBillAmt` | number \| null | Transportation bill amount |
| `advancePaid` | number \| null | Advance paid |
| `paymentTerms` | string \| null | Payment terms |
| `accDeptVerification` | string \| null | Accounts department verification |
| `netInwardQty` | number \| null | Net inward quantity |
| `rejection` | string \| null | Rejection |
| `shrinkageDump` | string \| null | Shrinkage / dump |
| `remarksPFL` | string \| null | Remarks (PFL) |
| `feedbackbyTransporterOwner` | string \| null | Feedback by transporter owner |

**No matching records:** `200` with `data: []` and a total of `0`.

**Errors:** `400` invalid filter · `401` not authenticated · `500` unexpected error (see [§ 5](#5-error-responses)).

#### Export Vehicle Dispatch

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/vehicleDispatches/export/excel` |
| **Purpose** | Downloads every Vehicle Dispatch document matching the filters as an `.xlsx` workbook. Same filters, search, sort and visibility as Get All; **no pagination**. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | Same row visibility as Get All. |
| **Path parameters** | None |

**Query parameters:** every Get All parameter above; `page` and `limit` are ignored.

<details><summary>Full parameter list</summary>

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page`, `limit` | number | No | **Ignored** — the export always contains every matching record. | — |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `dispatch.date` | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `dispatch.date` | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `vehicleType` | string (comma-separated) | No | Vehicle type (exact, case-insensitive). Exact, case-insensitive; any of the values | `abc` |
| `vehicleNo` | string | No | Vehicle number. Partial, case-insensitive | `MH12AB1234` |
| `driverName` | string | No | Driver name. Partial, case-insensitive | `Ramesh` |
| `customerName` | string | No | Client name. Partial, case-insensitive | `ABC Foods` |
| `deliveryChallanNo` | string | No | Delivery challan number. Partial, case-insensitive | `CN2026` |
| `clientGRNNo` | string | No | Client GRN number. Partial, case-insensitive | `abc` |
| `minAmount` | number | No | Transportation bill amount >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Transportation bill amount <= value. Inclusive upper bound | `50000` |

</details>

**Example request**

```http
GET /vehicleDispatches/export/excel?startDate=2026-09-01&endDate=2026-09-15&vehicleNo=MH12&driverName=Ramesh
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/vehicleDispatches/export/excel?startDate=2026-09-01&endDate=2026-09-15&vehicleNo=MH12&driverName=Ramesh" \
  -H "Authorization: Bearer <token>" \
  --output Vehicle_Dispatch.xlsx
```

**Response `200`**

```text
Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Content-Disposition: attachment; filename="Vehicle_Dispatch_2026-09-15.xlsx"; filename*=UTF-8''Vehicle_Dispatch_2026-09-15.xlsx
Cache-Control: no-store
X-Export-Record-Count: <documents in the file>
```

File name pattern: `Vehicle_Dispatch_<YYYY-MM-DD>.xlsx`. Sheets:

| Sheet | Columns | Column headers (in order) |
|---|---|---|
| `Vehicle Dispatch` | 46 | Dispatch No, Dispatch Record ID, Dispatch Date, Company, Company GST No, Vehicle Type, Vehicle No, Driver Name, Driver Mobile No, Reaching Time, Out Time, Client Name, Client Address, Receiving Person, Supervisor Name, Delivery Challan No, Delivery Challan Invoice Nos, Client GRN No, Net Inward Qty, Rejection, Shrinkage / Dump, Payment Discussed, Transportation Bill Amount, Advance Paid, Payment Terms, Accounts Dept Verification, Remarks (PFL), Feedback By Transporter Owner, Created Date, Updated Date, Document ID, Overall Status, Last Approval Stage, Last Approval Action, Last Action By, Last Action Date, Approved By, Approval Date, Rejected By, Rejection Date, Rejection Reason, Document Remarks, Document Created By, Document Created By Employee ID, Document Created Date, Document Updated Date |
| `Approvals` | 11 | Dispatch No, Record ID, Document ID, Overall Status, Stage, Stage Order, Action, Action By, Action By User ID, Reason, Action Date |

**Errors:** `400` invalid filter (JSON) · `401` not authenticated · `404 {"status":"fail","message":"No records found for the given filters"}` · `500` unexpected error.

#### Import Vehicle Dispatch

Not available — no import endpoint exists for this module (see [§ 7](#7-import-apis)).

---

### 8.15 Multi Cash Voucher

Document type `multi-cash-voucher` · Record table: `multiple_cash_voucher` · Document number: `voucherNo`

#### Get All Multi Cash Voucher

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/multiCashVoucher` |
| **Purpose** | Lists the Multi Cash Voucher documents visible to the user, filtered, searched, sorted and paginated. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | No role check. Visible to the document creator (any status); a **verifier** when the status is hold, VERIFIED, approved, FINALIZING, COMPLETE or REJECT; an **approver (levels 1–6)** when VERIFIED, approved, FINALIZING, COMPLETE or REJECT; a **first finalizer** when approved, FINALIZING (not yet first-finalized), COMPLETE or REJECT; a **second finalizer** when FINALIZING (already first-finalized), COMPLETE or REJECT. |
| **Implemented in** | `MultiCashVoucherController (GET /) → MultiCashVoucherService.getAllVouchers` |
| **Path parameters** | None |

**Query parameters** (all optional)

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page` | number | No | Page number. Applied only when **both** `page` and `limit` are given; otherwise every filtered row is returned (`totalPages` = 1). | `1` |
| `limit` | number | No | Records per page (see `page`). | `20` |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `multiple_cash_voucher.createdAt` (no voucher date column) | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `multiple_cash_voucher.createdAt` (no voucher date column) | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: location. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `approvalStatus` | enum (comma-separated) | No | Voucher approval status. Exact match; any of: `pending`, `approved`, `notapproved`, `active`, `incomplete`, `draft` | `pending` |
| `requestingDepartment` | enum (comma-separated) | No | Requesting department. Exact match; any of: `admin`, `hr`, `operations`, `sale`, `procurement`, `it`, `business_development`, `exports`, `branding_&_marketing`, `farming`, `quality_checking`, `other` | `admin` |
| `paymentMode` | string (comma-separated) | No | Payment mode (exact, case-insensitive). Exact, case-insensitive; any of the values | `cash` |
| `grnNo` | string | No | Linked GRN number. Partial, case-insensitive | `GRN2026` |
| `receiverName` | string | No | Receiver name. Partial, case-insensitive | `abc` |
| `payReceivedFrom` | string | No | Pay / received from. Partial, case-insensitive | `abc` |
| `debitCreditTo` | string | No | Debit / credit to. Partial, case-insensitive | `abc` |
| `minAmount` | number | No | Voucher amount >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Voucher amount <= value. Inclusive upper bound | `50000` |
| `deliveryChallanNo` | string | No | Delivery challan number. Partial, case-insensitive | `CN2026` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |

**Search fields** (`search`): document number (`voucherNo`), creator name, document status, company name, location name, GRN number, `receiverName`, `payReceivedFrom`, `debitCreditTo`, delivery challan number.

**Sortable in the database** (`sort`): `createdAt`, `createdDate`, `updatedAt`, `status`, `overAllStatus`, `documentNo`, `voucherNo`, `totalAmt`.

**Example request**

```http
GET /multiCashVoucher?startDate=2026-09-01&endDate=2026-09-15&approvalStatus=pending&paymentMode=cash&page=1&limit=20
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/multiCashVoucher?startDate=2026-09-01&endDate=2026-09-15&approvalStatus=pending&paymentMode=cash&page=1&limit=20" \
  -H "Authorization: Bearer <token>"
```

**Response `200`**

| Field | Type | Description |
|---|---|---|
| `status` | string | `"success"` |
| `data` | object[] | The records (fields below) |
| `allRecords` | number | Total matching records |
| `totalPages` | number | Total pages |
| `page` | number | Current page |

```json
{
  "status": "success",
  "data": [
    {
      "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "documentId": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "overAllStatus": "hold",
      "createdBy": "string",
      "createdDate": "2026-09-15",
      "createdTime": "10:30 AM",
      "voucherNo": "string",
      "companyName": "string",
      "location": "string",
      "grnNo": "string",
      "debitCreditTo": "string",
      "payReceivedFrom": "string",
      "paymentMode": "string",
      "receiverName": "string",
      "amtWords": "string",
      "remark": "string",
      "challanNo": "string",
      "totalAmt": "0.00"
    }
  ],
  "allRecords": 1,
  "totalPages": 1,
  "page": 1
}
```

`data[]` fields:

| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Record id of the module document |
| `documentId` | string (uuid) | Approval document id (`documents.id`), used by the `/view/:docid` endpoints |
| `overAllStatus` | string | Document workflow status (`documents.status`) |
| `createdBy` | string | Document creator's name |
| `createdDate` | string | Creation date, `YYYY-MM-DD` |
| `createdTime` | string | Creation time, `hh:mm A` |
| `voucherNo` | string | Voucher number |
| `companyName` | string \| null | Company name |
| `location` | string \| null | Location (branch) name |
| `grnNo` | string \| null | Linked GRN number |
| `debitCreditTo` | string | Debit / credit to |
| `payReceivedFrom` | string | Pay / received from |
| `paymentMode` | string | Payment mode |
| `receiverName` | string | Receiver name |
| `amtWords` | string | Amount in words |
| `remark` | string \| null | Remark |
| `challanNo` | string \| null | Linked delivery challan number |
| `totalAmt` | string \| null | Total amount (decimal, serialised as a string) |

**No matching records:** `200` with `data: []` and a total of `0`.

**Errors:** `400` invalid filter · `401` not authenticated · `500` unexpected error (see [§ 5](#5-error-responses)).

#### Export Multi Cash Voucher

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/multiCashVoucher/export/excel` |
| **Purpose** | Downloads every Multi Cash Voucher document matching the filters as an `.xlsx` workbook. Same filters, search, sort and visibility as Get All; **no pagination**. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | Same row visibility as Get All. |
| **Path parameters** | None |

**Query parameters:** every Get All parameter above; `page` and `limit` are ignored.

<details><summary>Full parameter list</summary>

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page`, `limit` | number | No | **Ignored** — the export always contains every matching record. | — |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `multiple_cash_voucher.createdAt` (no voucher date column) | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `multiple_cash_voucher.createdAt` (no voucher date column) | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: location. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `approvalStatus` | enum (comma-separated) | No | Voucher approval status. Exact match; any of: `pending`, `approved`, `notapproved`, `active`, `incomplete`, `draft` | `pending` |
| `requestingDepartment` | enum (comma-separated) | No | Requesting department. Exact match; any of: `admin`, `hr`, `operations`, `sale`, `procurement`, `it`, `business_development`, `exports`, `branding_&_marketing`, `farming`, `quality_checking`, `other` | `admin` |
| `paymentMode` | string (comma-separated) | No | Payment mode (exact, case-insensitive). Exact, case-insensitive; any of the values | `cash` |
| `grnNo` | string | No | Linked GRN number. Partial, case-insensitive | `GRN2026` |
| `receiverName` | string | No | Receiver name. Partial, case-insensitive | `abc` |
| `payReceivedFrom` | string | No | Pay / received from. Partial, case-insensitive | `abc` |
| `debitCreditTo` | string | No | Debit / credit to. Partial, case-insensitive | `abc` |
| `minAmount` | number | No | Voucher amount >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Voucher amount <= value. Inclusive upper bound | `50000` |
| `deliveryChallanNo` | string | No | Delivery challan number. Partial, case-insensitive | `CN2026` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |

</details>

**Example request**

```http
GET /multiCashVoucher/export/excel?startDate=2026-09-01&endDate=2026-09-15&approvalStatus=pending&paymentMode=cash
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/multiCashVoucher/export/excel?startDate=2026-09-01&endDate=2026-09-15&approvalStatus=pending&paymentMode=cash" \
  -H "Authorization: Bearer <token>" \
  --output Multi_Cash_Voucher.xlsx
```

**Response `200`**

```text
Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Content-Disposition: attachment; filename="Multi_Cash_Voucher_2026-09-15.xlsx"; filename*=UTF-8''Multi_Cash_Voucher_2026-09-15.xlsx
Cache-Control: no-store
X-Export-Record-Count: <documents in the file>
```

File name pattern: `Multi_Cash_Voucher_<YYYY-MM-DD>.xlsx`. Sheets:

| Sheet | Columns | Column headers (in order) |
|---|---|---|
| `Multi Cash Voucher` | 41 | Voucher No, Voucher Record ID, Requesting Department, Company, Company GST No, Location, GRN No, Debit / Credit To, Pay / Received From, Delivery Challan No, Total Amount, Payment Mode, Amount In Words, Receiver Name, Voucher Approval Status, Requested By, Requested By Employee ID, Passed By, Passed By Employee ID, Approved By (Voucher), Approved By (Voucher) Employee ID, Attachments, Remark, Created Date, Updated Date, Document ID, Overall Status, Last Approval Stage, Last Approval Action, Last Action By, Last Action Date, Approved By, Approval Date, Rejected By, Rejection Date, Rejection Reason, Document Remarks, Document Created By, Document Created By Employee ID, Document Created Date, Document Updated Date |
| `MCV Particulars` | 7 | Voucher No, Voucher Record ID, Particular ID, Description, Amount, Created Date, Updated Date |
| `Approvals` | 11 | Voucher No, Record ID, Document ID, Overall Status, Stage, Stage Order, Action, Action By, Action By User ID, Reason, Action Date |

**Errors:** `400` invalid filter (JSON) · `401` not authenticated · `404 {"status":"fail","message":"No records found for the given filters"}` · `500` unexpected error.

#### Import Multi Cash Voucher

Not available — no import endpoint exists for this module (see [§ 7](#7-import-apis)).

---

### 8.16 Labour Payment Voucher

Document type `labor-payment-voucher` · Record table: `labour_payment_voucher` · Document number: `voucherNo`

#### Get All Labour Payment Voucher

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/lpvoucher` |
| **Purpose** | Lists the Labour Payment Voucher documents visible to the user, filtered, searched, sorted and paginated. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | No role check. Visible to the document creator (any status); a **verifier** when the status is hold, VERIFIED, approved, FINALIZING, COMPLETE or REJECT; an **approver (levels 1–6)** when VERIFIED, approved, FINALIZING, COMPLETE or REJECT; a **first finalizer** when approved, FINALIZING (not yet first-finalized), COMPLETE or REJECT; a **second finalizer** when FINALIZING (already first-finalized), COMPLETE or REJECT. |
| **Implemented in** | `LPVoucherController (GET /) → LabourPaymentVoucherService.getLPVouchers` |
| **Path parameters** | None |

**Query parameters** (all optional)

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page` | number | No | Page number. Applied only when **both** `page` and `limit` are given; otherwise every filtered row is returned (`totalPages` = 1). | `1` |
| `limit` | number | No | Records per page (see `page`). | `20` |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `labour_payment_voucher.createdAt` (no voucher date column) | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `labour_payment_voucher.createdAt` (no voucher date column) | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: location. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `approvalStatus` | enum (comma-separated) | No | Voucher approval status. Exact match; any of: `pending`, `approved`, `notapproved`, `active`, `incomplete`, `draft` | `pending` |
| `requestingDepartment` | enum (comma-separated) | No | Requesting department. Exact match; any of: `admin`, `hr`, `operations`, `sale`, `procurement`, `it`, `business_development`, `exports`, `branding_&_marketing`, `farming`, `quality_checking`, `other` | `admin` |
| `paymentMode` | string (comma-separated) | No | Payment mode (exact, case-insensitive). Exact, case-insensitive; any of the values | `cash` |
| `grnNo` | string | No | Linked GRN number. Partial, case-insensitive | `GRN2026` |
| `receiverName` | string | No | Receiver name. Partial, case-insensitive | `abc` |
| `payReceivedFrom` | string | No | Pay / received from. Partial, case-insensitive | `abc` |
| `debitCreditTo` | string | No | Debit / credit to. Partial, case-insensitive | `abc` |
| `minAmount` | number | No | Voucher amount >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Voucher amount <= value. Inclusive upper bound | `50000` |
| `products` | string | No | Products text. Partial, case-insensitive | `abc` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |

**Search fields** (`search`): document number (`voucherNo`), creator name, document status, company name, location name, GRN number, `receiverName`, `payReceivedFrom`, `debitCreditTo`, `products`.

**Sortable in the database** (`sort`): `createdAt`, `createdDate`, `updatedAt`, `status`, `overAllStatus`, `documentNo`, `voucherNo`, `totalAmt`, `loadingDate`.

**Example request**

```http
GET /lpvoucher?startDate=2026-09-01&endDate=2026-09-15&status=hold&grnNo=GRN2026&page=1&limit=20
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/lpvoucher?startDate=2026-09-01&endDate=2026-09-15&status=hold&grnNo=GRN2026&page=1&limit=20" \
  -H "Authorization: Bearer <token>"
```

**Response `200`**

| Field | Type | Description |
|---|---|---|
| `status` | string | `"success"` |
| `data` | object[] | The records (fields below) |
| `allRecords` | number | Total matching records |
| `totalPages` | number | Total pages |
| `page` | number | Current page |

```json
{
  "status": "success",
  "data": [
    {
      "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "documentId": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "overAllStatus": "hold",
      "createdBy": "string",
      "createdDate": "2026-09-15",
      "createdTime": "10:30 AM",
      "voucherNo": "string",
      "companyName": "string",
      "location": "string",
      "grnNo": "string",
      "debitCreditTo": "string",
      "payReceivedFrom": "string",
      "paymentMode": "string",
      "receiverName": "string",
      "amtWords": "string",
      "remark": "string",
      "approvalStatus": "string",
      "requestingDepartment": "string",
      "loadingDate": "string",
      "noOfLabours": 0,
      "ratePerLabour": "0.00",
      "totalAmt": "0.00",
      "products": "string",
      "contactNo": "string",
      "altContactNo": "string",
      "kyc": false,
      "anyAttachment": [],
      "createdAt": "string",
      "updatedAt": "string",
      "deletionScheduledAt": "string",
      "isDeleted": false,
      "deletedAt": "string"
    }
  ],
  "allRecords": 1,
  "totalPages": 1,
  "page": 1
}
```

`data[]` fields:

| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Record id of the module document |
| `documentId` | string (uuid) | Approval document id (`documents.id`), used by the `/view/:docid` endpoints |
| `overAllStatus` | string | Document workflow status (`documents.status`) |
| `createdBy` | string | Document creator's name |
| `createdDate` | string | Creation date, `YYYY-MM-DD` |
| `createdTime` | string | Creation time, `hh:mm A` |
| `voucherNo` | string | Voucher number |
| `companyName` | string \| null | Company name |
| `location` | string \| null | Location (branch) name |
| `grnNo` | string \| null | Linked GRN number |
| `debitCreditTo` | string | Debit / credit to |
| `payReceivedFrom` | string | Pay / received from |
| `paymentMode` | string | Payment mode |
| `receiverName` | string | Receiver name |
| `amtWords` | string | Amount in words |
| `remark` | string \| null | Remark |
| `approvalStatus` | string \| null | Voucher approval status |
| `requestingDepartment` | string \| null | Requesting department |
| `loadingDate` | string \| null | Loading date |
| `noOfLabours` | number \| null | Number of labours |
| `ratePerLabour` | string \| null | Rate per labour (decimal, serialised as a string) |
| `totalAmt` | string \| null | Total amount (decimal, serialised as a string) |
| `products` | string \| null | Products (free text) |
| `contactNo` | string \| null | Contact number |
| `altContactNo` | string \| null | Alternate contact number |
| `kyc` | boolean \| null | KYC done |
| `anyAttachment` | string[] \| null | Attachment URLs |
| `createdAt` | string | Record created timestamp (ISO 8601) |
| `updatedAt` | string | Record updated timestamp (ISO 8601) |
| `deletionScheduledAt` | string \| null | Scheduled deletion timestamp |
| `isDeleted` | boolean | Soft-delete flag |
| `deletedAt` | string \| null | Soft-delete timestamp |

**No matching records:** `200` with `data: []` and a total of `0`.

**Errors:** `400` invalid filter · `401` not authenticated · `500` unexpected error (see [§ 5](#5-error-responses)).

#### Export Labour Payment Voucher

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/lpvoucher/export/excel` |
| **Purpose** | Downloads every Labour Payment Voucher document matching the filters as an `.xlsx` workbook. Same filters, search, sort and visibility as Get All; **no pagination**. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | Same row visibility as Get All. |
| **Path parameters** | None |

**Query parameters:** every Get All parameter above; `page` and `limit` are ignored.

<details><summary>Full parameter list</summary>

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page`, `limit` | number | No | **Ignored** — the export always contains every matching record. | — |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `labour_payment_voucher.createdAt` (no voucher date column) | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `labour_payment_voucher.createdAt` (no voucher date column) | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: location. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `approvalStatus` | enum (comma-separated) | No | Voucher approval status. Exact match; any of: `pending`, `approved`, `notapproved`, `active`, `incomplete`, `draft` | `pending` |
| `requestingDepartment` | enum (comma-separated) | No | Requesting department. Exact match; any of: `admin`, `hr`, `operations`, `sale`, `procurement`, `it`, `business_development`, `exports`, `branding_&_marketing`, `farming`, `quality_checking`, `other` | `admin` |
| `paymentMode` | string (comma-separated) | No | Payment mode (exact, case-insensitive). Exact, case-insensitive; any of the values | `cash` |
| `grnNo` | string | No | Linked GRN number. Partial, case-insensitive | `GRN2026` |
| `receiverName` | string | No | Receiver name. Partial, case-insensitive | `abc` |
| `payReceivedFrom` | string | No | Pay / received from. Partial, case-insensitive | `abc` |
| `debitCreditTo` | string | No | Debit / credit to. Partial, case-insensitive | `abc` |
| `minAmount` | number | No | Voucher amount >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Voucher amount <= value. Inclusive upper bound | `50000` |
| `products` | string | No | Products text. Partial, case-insensitive | `abc` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |

</details>

**Example request**

```http
GET /lpvoucher/export/excel?startDate=2026-09-01&endDate=2026-09-15&status=hold&grnNo=GRN2026
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/lpvoucher/export/excel?startDate=2026-09-01&endDate=2026-09-15&status=hold&grnNo=GRN2026" \
  -H "Authorization: Bearer <token>" \
  --output Labour_Payment_Voucher.xlsx
```

**Response `200`**

```text
Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Content-Disposition: attachment; filename="Labour_Payment_Voucher_2026-09-15.xlsx"; filename*=UTF-8''Labour_Payment_Voucher_2026-09-15.xlsx
Cache-Control: no-store
X-Export-Record-Count: <documents in the file>
```

File name pattern: `Labour_Payment_Voucher_<YYYY-MM-DD>.xlsx`. Sheets:

| Sheet | Columns | Column headers (in order) |
|---|---|---|
| `Labour Payment Voucher` | 47 | Voucher No, Voucher Record ID, Requesting Department, Company, Company GST No, Location, GRN No, Debit / Credit To, Pay / Received From, Loading Date, Products, No Of Labours, Rate Per Labour, Total Amount, Contact No, Alternate Contact No, KYC Done, Payment Mode, Amount In Words, Receiver Name, Voucher Approval Status, Requested By, Requested By Employee ID, Passed By, Passed By Employee ID, Approved By (Voucher), Approved By (Voucher) Employee ID, Attachments, Remark, Created Date, Updated Date, Document ID, Overall Status, Last Approval Stage, Last Approval Action, Last Action By, Last Action Date, Approved By, Approval Date, Rejected By, Rejection Date, Rejection Reason, Document Remarks, Document Created By, Document Created By Employee ID, Document Created Date, Document Updated Date |
| `Approvals` | 11 | Voucher No, Record ID, Document ID, Overall Status, Stage, Stage Order, Action, Action By, Action By User ID, Reason, Action Date |

**Errors:** `400` invalid filter (JSON) · `401` not authenticated · `404 {"status":"fail","message":"No records found for the given filters"}` · `500` unexpected error.

#### Import Labour Payment Voucher

Not available — no import endpoint exists for this module (see [§ 7](#7-import-apis)).

---

### 8.17 Transport Payment Voucher

Document type `transport-payment-voucher` · Record table: `transport_payment_voucher` · Document number: `voucherNo`

#### Get All Transport Payment Voucher

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/tpvoucher` |
| **Purpose** | Lists the Transport Payment Voucher documents visible to the user, filtered, searched, sorted and paginated. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | No role check. Visible to the document creator (any status); a **verifier** when the status is hold, VERIFIED, approved, FINALIZING, COMPLETE or REJECT; an **approver (levels 1–6)** when VERIFIED, approved, FINALIZING, COMPLETE or REJECT; a **first finalizer** when approved, FINALIZING (not yet first-finalized), COMPLETE or REJECT; a **second finalizer** when FINALIZING (already first-finalized), COMPLETE or REJECT. |
| **Implemented in** | `TPVoucherController (GET /) → TPVoucherService.getAllTPVouchers` |
| **Path parameters** | None |

**Query parameters** (all optional)

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page` | number | No | Page number. Applied only when **both** `page` and `limit` are given; otherwise every filtered row is returned (`totalPages` = 1). | `1` |
| `limit` | number | No | Records per page (see `page`). | `20` |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `transport_payment_voucher.createdAt` (no voucher date column) | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `transport_payment_voucher.createdAt` (no voucher date column) | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: location. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `approvalStatus` | enum (comma-separated) | No | Voucher approval status. Exact match; any of: `pending`, `approved`, `notapproved`, `active`, `incomplete`, `draft` | `pending` |
| `requestingDepartment` | enum (comma-separated) | No | Requesting department. Exact match; any of: `admin`, `hr`, `operations`, `sale`, `procurement`, `it`, `business_development`, `exports`, `branding_&_marketing`, `farming`, `quality_checking`, `other` | `admin` |
| `paymentMode` | string (comma-separated) | No | Payment mode (exact, case-insensitive). Exact, case-insensitive; any of the values | `cash` |
| `grnNo` | string | No | Linked GRN number. Partial, case-insensitive | `GRN2026` |
| `receiverName` | string | No | Receiver name. Partial, case-insensitive | `abc` |
| `payReceivedFrom` | string | No | Pay / received from. Partial, case-insensitive | `abc` |
| `debitCreditTo` | string | No | Debit / credit to. Partial, case-insensitive | `abc` |
| `minAmount` | number | No | Voucher amount >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Voucher amount <= value. Inclusive upper bound | `50000` |
| `vehicleNo` | string | No | Vehicle number. Partial, case-insensitive | `MH12AB1234` |
| `driverName` | string | No | Driver name. Partial, case-insensitive | `Ramesh` |
| `dispatchLocation` | string | No | Dispatch location. Partial, case-insensitive | `abc` |
| `destinationLocation` | string | No | Destination location. Partial, case-insensitive | `abc` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |

**Search fields** (`search`): document number (`voucherNo`), creator name, document status, company name, location name, GRN number, `receiverName`, `payReceivedFrom`, `debitCreditTo`, `vehicleNo`, `driverName`, `dispatchLocation`, `destinationLocation`, product name / code.

**Sortable in the database** (`sort`): `createdAt`, `createdDate`, `updatedAt`, `status`, `overAllStatus`, `documentNo`, `voucherNo`, `finalPayableAmt`.

**Example request**

```http
GET /tpvoucher?startDate=2026-09-01&endDate=2026-09-15&vehicleNo=MH12&minAmount=500&maxAmount=5000&page=1&limit=20
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/tpvoucher?startDate=2026-09-01&endDate=2026-09-15&vehicleNo=MH12&minAmount=500&maxAmount=5000&page=1&limit=20" \
  -H "Authorization: Bearer <token>"
```

**Response `200`**

| Field | Type | Description |
|---|---|---|
| `status` | string | `"success"` |
| `data` | object[] | The records (fields below) |
| `allRecords` | number | Total matching records |
| `totalPages` | number | Total pages |
| `page` | number | Current page |

```json
{
  "status": "success",
  "data": [
    {
      "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "documentId": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "overAllStatus": "hold",
      "createdBy": "string",
      "createdDate": "2026-09-15",
      "createdTime": "10:30 AM",
      "voucherNo": "string",
      "companyName": "string",
      "location": "string",
      "grnNo": "string",
      "debitCreditTo": "string",
      "payReceivedFrom": "string",
      "paymentMode": "string",
      "receiverName": "string",
      "amtWords": "string",
      "remark": "string",
      "vehicleNo": "string",
      "driverName": "string",
      "contactNo": "string",
      "altContactNo": "string",
      "dispatchLocation": "string",
      "destinationLocation": "string",
      "freightAmt": "0.00",
      "totalAmt": "0.00",
      "kyc": false
    }
  ],
  "allRecords": 1,
  "totalPages": 1,
  "page": 1
}
```

`data[]` fields:

| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Record id of the module document |
| `documentId` | string (uuid) | Approval document id (`documents.id`), used by the `/view/:docid` endpoints |
| `overAllStatus` | string | Document workflow status (`documents.status`) |
| `createdBy` | string | Document creator's name |
| `createdDate` | string | Creation date, `YYYY-MM-DD` |
| `createdTime` | string | Creation time, `hh:mm A` |
| `voucherNo` | string | Voucher number |
| `companyName` | string \| null | Company name |
| `location` | string \| null | Location (branch) name |
| `grnNo` | string \| null | Linked GRN number |
| `debitCreditTo` | string | Debit / credit to |
| `payReceivedFrom` | string | Pay / received from |
| `paymentMode` | string | Payment mode |
| `receiverName` | string | Receiver name |
| `amtWords` | string | Amount in words |
| `remark` | string \| null | Remark |
| `vehicleNo` | string \| null | Vehicle number |
| `driverName` | string \| null | Driver name |
| `contactNo` | string \| null | Contact number |
| `altContactNo` | string \| null | Alternate contact number |
| `dispatchLocation` | string \| null | Dispatch location |
| `destinationLocation` | string \| null | Destination location |
| `freightAmt` | string \| null | Freight amount (decimal, serialised as a string) |
| `totalAmt` | string \| null | Total amount (decimal, serialised as a string) |
| `kyc` | boolean \| null | KYC done |

**No matching records:** `200` with `data: []` and a total of `0`.

**Errors:** `400` invalid filter · `401` not authenticated · `500` unexpected error (see [§ 5](#5-error-responses)).

#### Export Transport Payment Voucher

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/tpvoucher/export/excel` |
| **Purpose** | Downloads every Transport Payment Voucher document matching the filters as an `.xlsx` workbook. Same filters, search, sort and visibility as Get All; **no pagination**. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | Same row visibility as Get All. |
| **Path parameters** | None |

**Query parameters:** every Get All parameter above; `page` and `limit` are ignored.

<details><summary>Full parameter list</summary>

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page`, `limit` | number | No | **Ignored** — the export always contains every matching record. | — |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `transport_payment_voucher.createdAt` (no voucher date column) | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `transport_payment_voucher.createdAt` (no voucher date column) | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: location. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `approvalStatus` | enum (comma-separated) | No | Voucher approval status. Exact match; any of: `pending`, `approved`, `notapproved`, `active`, `incomplete`, `draft` | `pending` |
| `requestingDepartment` | enum (comma-separated) | No | Requesting department. Exact match; any of: `admin`, `hr`, `operations`, `sale`, `procurement`, `it`, `business_development`, `exports`, `branding_&_marketing`, `farming`, `quality_checking`, `other` | `admin` |
| `paymentMode` | string (comma-separated) | No | Payment mode (exact, case-insensitive). Exact, case-insensitive; any of the values | `cash` |
| `grnNo` | string | No | Linked GRN number. Partial, case-insensitive | `GRN2026` |
| `receiverName` | string | No | Receiver name. Partial, case-insensitive | `abc` |
| `payReceivedFrom` | string | No | Pay / received from. Partial, case-insensitive | `abc` |
| `debitCreditTo` | string | No | Debit / credit to. Partial, case-insensitive | `abc` |
| `minAmount` | number | No | Voucher amount >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Voucher amount <= value. Inclusive upper bound | `50000` |
| `vehicleNo` | string | No | Vehicle number. Partial, case-insensitive | `MH12AB1234` |
| `driverName` | string | No | Driver name. Partial, case-insensitive | `Ramesh` |
| `dispatchLocation` | string | No | Dispatch location. Partial, case-insensitive | `abc` |
| `destinationLocation` | string | No | Destination location. Partial, case-insensitive | `abc` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line item (the document is returned once). Partial, case-insensitive | `PRD001` |
| `productName` | string | No | Product name on any line item (the document is returned once). Partial, case-insensitive | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line item (the document is returned once). Partial, case-insensitive | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line item (the document is returned once) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line item (the document is returned once). Partial, case-insensitive | `Vegetables` |

</details>

**Example request**

```http
GET /tpvoucher/export/excel?startDate=2026-09-01&endDate=2026-09-15&vehicleNo=MH12&minAmount=500&maxAmount=5000
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/tpvoucher/export/excel?startDate=2026-09-01&endDate=2026-09-15&vehicleNo=MH12&minAmount=500&maxAmount=5000" \
  -H "Authorization: Bearer <token>" \
  --output Transport_Payment_Voucher.xlsx
```

**Response `200`**

```text
Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Content-Disposition: attachment; filename="Transport_Payment_Voucher_2026-09-15.xlsx"; filename*=UTF-8''Transport_Payment_Voucher_2026-09-15.xlsx
Cache-Control: no-store
X-Export-Record-Count: <documents in the file>
```

File name pattern: `Transport_Payment_Voucher_<YYYY-MM-DD>.xlsx`. Sheets:

| Sheet | Columns | Column headers (in order) |
|---|---|---|
| `Transport Payment Voucher` | 55 | Voucher No, Voucher Record ID, Requesting Department, Company, Company GST No, Location, GRN No, Debit / Credit To, Pay / Received From, Vehicle No, Driver Name, Contact No, Alternate Contact No, Dispatch Location, Destination Location, Products, Freight Amount, Decided Amount, Actual Amount, Advance Amount, Total Payable Amount, Deduction Amount, Extra Amount, Final Payable Amount, KYC Done, Payment Mode, Amount In Words, Receiver Name, Voucher Approval Status, Requested By, Requested By Employee ID, Passed By, Passed By Employee ID, Approved By (Voucher), Approved By (Voucher) Employee ID, Attachments, Remark, Created Date, Updated Date, Document ID, Overall Status, Last Approval Stage, Last Approval Action, Last Action By, Last Action Date, Approved By, Approval Date, Rejected By, Rejection Date, Rejection Reason, Document Remarks, Document Created By, Document Created By Employee ID, Document Created Date, Document Updated Date |
| `TPV Products` | 6 | Voucher No, Voucher Record ID, Product ID, Product Code, Product Name, Category |
| `Approvals` | 11 | Voucher No, Record ID, Document ID, Overall Status, Stage, Stage Order, Action, Action By, Action By User ID, Reason, Action Date |

**Errors:** `400` invalid filter (JSON) · `401` not authenticated · `404 {"status":"fail","message":"No records found for the given filters"}` · `500` unexpected error.

#### Import Transport Payment Voucher

Not available — no import endpoint exists for this module (see [§ 7](#7-import-apis)).

---

### 8.18 Packing Material Voucher

Document type `packaging-material-voucher` · Record table: `packing_material_payment` · Document number: `voucherNo`

#### Get All Packing Material Voucher

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/pmpvoucher` |
| **Purpose** | Lists the Packing Material Voucher documents visible to the user, filtered, searched, sorted and paginated. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | No role check. Visible to the document creator (any status); a **verifier** when the status is hold, VERIFIED, approved, FINALIZING, COMPLETE or REJECT; an **approver (levels 1–6)** when VERIFIED, approved, FINALIZING, COMPLETE or REJECT; a **first finalizer** when approved, FINALIZING (not yet first-finalized), COMPLETE or REJECT; a **second finalizer** when FINALIZING (already first-finalized), COMPLETE or REJECT. |
| **Implemented in** | `PMPVoucherController (GET /) → PMPVoucherService.getAllVouchers` |
| **Path parameters** | None |

**Query parameters** (all optional)

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page` | number | No | Page number. Applied only when **both** `page` and `limit` are given; otherwise every filtered row is returned (`totalPages` = 1). | `1` |
| `limit` | number | No | Records per page (see `page`). | `20` |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `packing_material_payment.createdAt` (no voucher date column) | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `packing_material_payment.createdAt` (no voucher date column) | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: location. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `approvalStatus` | enum (comma-separated) | No | Voucher approval status. Exact match; any of: `pending`, `approved`, `notapproved`, `active`, `incomplete`, `draft` | `pending` |
| `requestingDepartment` | enum (comma-separated) | No | Requesting department. Exact match; any of: `admin`, `hr`, `operations`, `sale`, `procurement`, `it`, `business_development`, `exports`, `branding_&_marketing`, `farming`, `quality_checking`, `other` | `admin` |
| `paymentMode` | string (comma-separated) | No | Payment mode (exact, case-insensitive). Exact, case-insensitive; any of the values | `cash` |
| `grnNo` | string | No | Linked GRN number. Partial, case-insensitive | `GRN2026` |
| `receiverName` | string | No | Receiver name. Partial, case-insensitive | `abc` |
| `payReceivedFrom` | string | No | Pay / received from. Partial, case-insensitive | `abc` |
| `debitCreditTo` | string | No | Debit / credit to. Partial, case-insensitive | `abc` |
| `minAmount` | number | No | Voucher amount >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Voucher amount <= value. Inclusive upper bound | `50000` |
| `sellerName` | string | No | Seller name. Partial, case-insensitive | `abc` |
| `purpose` | string | No | Purpose. Partial, case-insensitive | `abc` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |

**Search fields** (`search`): document number (`voucherNo`), creator name, document status, company name, location name, GRN number, `receiverName`, `payReceivedFrom`, `debitCreditTo`, `sellerName`, `purpose`.

**Sortable in the database** (`sort`): `createdAt`, `createdDate`, `updatedAt`, `status`, `overAllStatus`, `documentNo`, `voucherNo`, `totalAmt`.

**Example request**

```http
GET /pmpvoucher?startDate=2026-09-01&endDate=2026-09-15&sellerName=Packaging&approvalStatus=approved&page=1&limit=20
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/pmpvoucher?startDate=2026-09-01&endDate=2026-09-15&sellerName=Packaging&approvalStatus=approved&page=1&limit=20" \
  -H "Authorization: Bearer <token>"
```

**Response `200`**

| Field | Type | Description |
|---|---|---|
| `status` | string | `"success"` |
| `data` | object[] | The records (fields below) |
| `allRecords` | number | Total matching records |
| `totalPages` | number | Total pages |
| `page` | number | Current page |

```json
{
  "status": "success",
  "data": [
    {
      "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "documentId": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "overAllStatus": "hold",
      "createdBy": "string",
      "createdDate": "2026-09-15",
      "createdTime": "10:30 AM",
      "voucherNo": "string",
      "companyName": "string",
      "location": "string",
      "grnNo": "string",
      "debitCreditTo": "string",
      "payReceivedFrom": "string",
      "paymentMode": "string",
      "receiverName": "string",
      "amtWords": "string",
      "remark": "string",
      "approvalStatus": "string",
      "requestingDepartment": "string",
      "sellerName": "string",
      "address": {
        "address1": "string",
        "address2": "string",
        "location": "string",
        "city": "string",
        "state": "string",
        "pincode": "string"
      },
      "contactNo": "string",
      "altContactNo": "string",
      "purpose": "string",
      "totalAmt": "0.00",
      "kyc": false
    }
  ],
  "allRecords": 1,
  "totalPages": 1,
  "page": 1
}
```

`data[]` fields:

| Field | Type | Description |
|---|---|---|
| `id` | string (uuid) | Record id of the module document |
| `documentId` | string (uuid) | Approval document id (`documents.id`), used by the `/view/:docid` endpoints |
| `overAllStatus` | string | Document workflow status (`documents.status`) |
| `createdBy` | string | Document creator's name |
| `createdDate` | string | Creation date, `YYYY-MM-DD` |
| `createdTime` | string | Creation time, `hh:mm A` |
| `voucherNo` | string | Voucher number |
| `companyName` | string \| null | Company name |
| `location` | string \| null | Location (branch) name |
| `grnNo` | string \| null | Linked GRN number |
| `debitCreditTo` | string | Debit / credit to |
| `payReceivedFrom` | string | Pay / received from |
| `paymentMode` | string | Payment mode |
| `receiverName` | string | Receiver name |
| `amtWords` | string | Amount in words |
| `remark` | string \| null | Remark |
| `approvalStatus` | string \| null | Voucher approval status |
| `requestingDepartment` | string \| null | Requesting department |
| `sellerName` | string \| null | Seller name |
| `address` | object \| null | Seller address (fields as below, without id) |
| `contactNo` | string \| null | Contact number |
| `altContactNo` | string \| null | Alternate contact number |
| `purpose` | string \| null | Purpose |
| `totalAmt` | string \| null | Total amount (decimal, serialised as a string) |
| `kyc` | boolean \| null | KYC done |

`address` fields:

| Field | Type | Description |
|---|---|---|
| `address1` | string | Address line 1 |
| `address2` | string \| null | Address line 2 |
| `location` | string \| null | Locality |
| `city` | string | City |
| `state` | string | State |
| `pincode` | string | PIN code |

**No matching records:** `200` with `data: []` and a total of `0`.

**Errors:** `400` invalid filter · `401` not authenticated · `500` unexpected error (see [§ 5](#5-error-responses)).

#### Export Packing Material Voucher

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/pmpvoucher/export/excel` |
| **Purpose** | Downloads every Packing Material Voucher document matching the filters as an `.xlsx` workbook. Same filters, search, sort and visibility as Get All; **no pagination**. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | Same row visibility as Get All. |
| **Path parameters** | None |

**Query parameters:** every Get All parameter above; `page` and `limit` are ignored.

<details><summary>Full parameter list</summary>

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page`, `limit` | number | No | **Ignored** — the export always contains every matching record. | — |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on `packing_material_payment.createdAt` (no voucher date column) | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on `packing_material_payment.createdAt` (no voucher date column) | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status. Exact; any of: `hold`, `query`, `approved`, `disapproved`, `FINALIZING`, `FINALIZED`, `COMPLETE`, `REJECT`, `VERIFIED` | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `sortBy`, `sortOrder` | string | No | Alternative to `sort` (`sortOrder` = `ASC` or `DESC`) | `createdAt`, `DESC` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive | `Prime Fresh` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: location. Exact match; any of the ids | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive | `Pune` |
| `approvalStatus` | enum (comma-separated) | No | Voucher approval status. Exact match; any of: `pending`, `approved`, `notapproved`, `active`, `incomplete`, `draft` | `pending` |
| `requestingDepartment` | enum (comma-separated) | No | Requesting department. Exact match; any of: `admin`, `hr`, `operations`, `sale`, `procurement`, `it`, `business_development`, `exports`, `branding_&_marketing`, `farming`, `quality_checking`, `other` | `admin` |
| `paymentMode` | string (comma-separated) | No | Payment mode (exact, case-insensitive). Exact, case-insensitive; any of the values | `cash` |
| `grnNo` | string | No | Linked GRN number. Partial, case-insensitive | `GRN2026` |
| `receiverName` | string | No | Receiver name. Partial, case-insensitive | `abc` |
| `payReceivedFrom` | string | No | Pay / received from. Partial, case-insensitive | `abc` |
| `debitCreditTo` | string | No | Debit / credit to. Partial, case-insensitive | `abc` |
| `minAmount` | number | No | Voucher amount >= value. Inclusive lower bound | `1000` |
| `maxAmount` | number | No | Voucher amount <= value. Inclusive upper bound | `50000` |
| `sellerName` | string | No | Seller name. Partial, case-insensitive | `abc` |
| `purpose` | string | No | Purpose. Partial, case-insensitive | `abc` |
| `warehouseId`, `warehouse` | uuid / string | No | Aliases of `locationId` / `location` (branches are the storage locations) | — |

</details>

**Example request**

```http
GET /pmpvoucher/export/excel?startDate=2026-09-01&endDate=2026-09-15&sellerName=Packaging&approvalStatus=approved
Authorization: Bearer <token>
```

```bash
curl -X GET \
  "http://localhost:4000/pmpvoucher/export/excel?startDate=2026-09-01&endDate=2026-09-15&sellerName=Packaging&approvalStatus=approved" \
  -H "Authorization: Bearer <token>" \
  --output Packing_Material_Voucher.xlsx
```

**Response `200`**

```text
Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Content-Disposition: attachment; filename="Packing_Material_Voucher_2026-09-15.xlsx"; filename*=UTF-8''Packing_Material_Voucher_2026-09-15.xlsx
Cache-Control: no-store
X-Export-Record-Count: <documents in the file>
```

File name pattern: `Packing_Material_Voucher_<YYYY-MM-DD>.xlsx`. Sheets:

| Sheet | Columns | Column headers (in order) |
|---|---|---|
| `Packing Material Voucher` | 46 | Voucher No, Voucher Record ID, Requesting Department, Company, Company GST No, Location, GRN No, Debit / Credit To, Pay / Received From, Seller Name, Seller Address, Contact No, Alternate Contact No, Purpose, Total Amount, KYC Done, Payment Mode, Amount In Words, Receiver Name, Voucher Approval Status, Requested By, Requested By Employee ID, Passed By, Passed By Employee ID, Approved By (Voucher), Approved By (Voucher) Employee ID, Attachments, Remark, Created Date, Updated Date, Document ID, Overall Status, Last Approval Stage, Last Approval Action, Last Action By, Last Action Date, Approved By, Approval Date, Rejected By, Rejection Date, Rejection Reason, Document Remarks, Document Created By, Document Created By Employee ID, Document Created Date, Document Updated Date |
| `PMPV Materials` | 10 | Voucher No, Voucher Record ID, Material ID, Item Name, Item Quantity, Item UOM, Rate, Amount, Created Date, Updated Date |
| `Approvals` | 11 | Voucher No, Record ID, Document ID, Overall Status, Stage, Stage Order, Action, Action By, Action By User ID, Reason, Action Date |

**Errors:** `400` invalid filter (JSON) · `401` not authenticated · `404 {"status":"fail","message":"No records found for the given filters"}` · `500` unexpected error.

#### Import Packing Material Voucher

Not available — no import endpoint exists for this module (see [§ 7](#7-import-apis)).

---

### 8.19 All Vouchers

All four voucher types (Multi Cash, Labour Payment, Transport Payment and Packing Material) in one list or one workbook. Each type is read through its own list service with the same common filters, so visibility and filtering match the four voucher lists above.

#### Get All Vouchers

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/vouchers` |
| **Purpose** | Merged list of every voucher type visible to the user, filtered and sorted, optionally paginated. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | No role check. Per voucher type, visible to the document creator (any status); a **verifier** when the status is hold, VERIFIED, approved, FINALIZING, COMPLETE or REJECT; an **approver (levels 1–6)** when VERIFIED, approved, FINALIZING, COMPLETE or REJECT; a **first finalizer** when approved, FINALIZING (not yet first-finalized), COMPLETE or REJECT; a **second finalizer** when FINALIZING (already first-finalized), COMPLETE or REJECT. |
| **Implemented in** | `VouchersController.getAllVouchers` |
| **Path parameters** | None |

**Query parameters** (all optional)

| Parameter | Type | Required | Description | Example |
|---|---|---|---|---|
| `page` | number | No | Page number. Pagination is applied only when both `page` and `limit` are given. | `1` |
| `limit` | number | No | Records per page | `20` |
| `voucherType` | enum (comma-separated) | No | Voucher types to include; any of: `multi-cash-voucher`, `labour-payment-voucher`, `transport-payment-voucher`, `packing-material-voucher` | `multi-cash-voucher,transport-payment-voucher` |
| `startDate` | date `YYYY-MM-DD` | No | Business date >= this day (YYYY-MM-DD, inclusive). Alias: dateFrom. Filters on the voucher creation date | `2026-09-01` |
| `endDate` | date `YYYY-MM-DD` | No | Business date <= this day (YYYY-MM-DD, inclusive). Alias: dateTo. Filters on the voucher creation date | `2026-09-15` |
| `status` | enum (comma-separated) | No | Document workflow status, comma separated (documents.status) | `COMPLETE,hold` |
| `search` | string | No | Partial, case-insensitive match on the document number, creator and the module search fields | `ABC` |
| `documentNo` | string | No | The module's own document number (partial) | `0012` |
| `createdById` | uuid (comma-separated) | No | Document creator user id(s) | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `createdBy` | string | No | Document creator name (partial) | `Asha` |
| `approvedById` | uuid (comma-separated) | No | User id(s) who approved or verified at any stage | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `approvedBy` | string | No | Name of a user who approved or verified at any stage (partial) | `Ravi` |
| `approvalStartDate` | date `YYYY-MM-DD` | No | An approval/verification action on or after this day (YYYY-MM-DD) | `2026-09-01` |
| `approvalEndDate` | date `YYYY-MM-DD` | No | An approval/verification action on or before this day (YYYY-MM-DD) | `2026-09-15` |
| `sort` | string `field:ASC\|DESC` | No | field:ASC\|DESC (existing convention). Aliases: sortBy + sortOrder | `createdAt:DESC` |
| `companyId` | uuid (comma-separated) | No | Company id. Exact match; any of the ids. | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `company` | string | No | Company name. Partial, case-insensitive. | `Prime Fresh` |
| `locationId` | uuid (comma-separated) | No | Location (branch) id, on: location. Exact match; any of the ids. | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `location` | string | No | Location name. Partial, case-insensitive. | `Pune` |
| `approvalStatus` | enum (comma-separated) | No | Voucher approval status. Exact match; any of: `pending`, `approved`, `notapproved`, `active`, `incomplete`, `draft`. | `pending` |
| `requestingDepartment` | enum (comma-separated) | No | Requesting department. Exact match; any of: `admin`, `hr`, `operations`, `sale`, `procurement`, `it`, `business_development`, `exports`, `branding_&_marketing`, `farming`, `quality_checking`, `other`. | `admin` |
| `paymentMode` | string (comma-separated) | No | Payment mode (exact, case-insensitive). Exact, case-insensitive; any of the values. | `cash` |
| `grnNo` | string | No | Linked GRN number. Partial, case-insensitive. | `GRN2026` |
| `receiverName` | string | No | Receiver name. Partial, case-insensitive. | `abc` |
| `payReceivedFrom` | string | No | Pay / received from. Partial, case-insensitive. | `abc` |
| `debitCreditTo` | string | No | Debit / credit to. Partial, case-insensitive. | `abc` |
| `minAmount` | number | No | Voucher amount >= value. Inclusive lower bound. | `1000` |
| `maxAmount` | number | No | Voucher amount <= value. Inclusive upper bound. | `50000` |
| `deliveryChallanNo` | string | No | Delivery challan number. Partial, case-insensitive. Only Multi Cash Voucher  this field; other voucher types are excluded when it is used. | `CN2026` |
| `products` | string | No | Products text. Partial, case-insensitive. Only Labour Payment Voucher  this field; other voucher types are excluded when it is used. | `abc` |
| `vehicleNo` | string | No | Vehicle number. Partial, case-insensitive. Only Transport Payment Voucher  this field; other voucher types are excluded when it is used. | `MH12AB1234` |
| `driverName` | string | No | Driver name. Partial, case-insensitive. Only Transport Payment Voucher  this field; other voucher types are excluded when it is used. | `Ramesh` |
| `dispatchLocation` | string | No | Dispatch location. Partial, case-insensitive. Only Transport Payment Voucher  this field; other voucher types are excluded when it is used. | `abc` |
| `destinationLocation` | string | No | Destination location. Partial, case-insensitive. Only Transport Payment Voucher  this field; other voucher types are excluded when it is used. | `abc` |
| `sellerName` | string | No | Seller name. Partial, case-insensitive. Only Packing Material Voucher  this field; other voucher types are excluded when it is used. | `abc` |
| `purpose` | string | No | Purpose. Partial, case-insensitive. Only Packing Material Voucher  this field; other voucher types are excluded when it is used. | `abc` |
| `productId` | uuid (comma-separated) | No | Product id(s) on any line. Only Transport Payment Vouchers have products; other types are excluded when used. | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `productCode` | string | No | Product code on any line (partial). Only Transport Payment Vouchers have products; other types are excluded when used. | `PRD001` |
| `productName` | string | No | Product name on any line (partial). Only Transport Payment Vouchers have products; other types are excluded when used. | `Tomato` |
| `variantId` | uuid (comma-separated) | No | Variant id(s) on any line. Only Transport Payment Vouchers have products; other types are excluded when used. | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `variant` | string | No | Variant name on any line (partial). Only Transport Payment Vouchers have products; other types are excluded when used. | `Hybrid` |
| `categoryId` | uuid (comma-separated) | No | Product category id(s) on any line. Only Transport Payment Vouchers have products; other types are excluded when used. | `4c7e1a2b-3d4e-4f5a-8b6c-7d8e9f0a1b2c` |
| `category` | string | No | Product category name on any line (partial). Only Transport Payment Vouchers have products; other types are excluded when used. | `Vegetables` |

**Sorting:** `sort` (or `sortBy` + `sortOrder`) on any field of the returned rows; without it, newest documents first.

**Example request**

```bash
curl -X GET \
  "http://localhost:4000/vouchers?startDate=2026-09-01&endDate=2026-09-15&voucherType=multi-cash-voucher,transport-payment-voucher&status=hold&page=1&limit=20" \
  -H "Authorization: Bearer <token>"
```

**Response `200`**

| Field | Type | Description |
|---|---|---|
| `status` | string | `"success"` |
| `data` | object[] | Rows of each voucher type’s own list (see § 8.15–8.18), plus the fields below |
| `data[].voucherType` | string | One of `multi-cash-voucher`, `labour-payment-voucher`, `transport-payment-voucher`, `packing-material-voucher` |
| `data[].voucherTypeLabel` | string | Readable voucher type, e.g. `Transport Payment Voucher` |
| `allRecords` | number | Total matching records |
| `totalPages` | number | Total pages (1 when not paginated) |
| `page` | number | Current page (1 when not paginated) |

```json
{
  "status": "success",
  "data": [
    {
      "id": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "documentId": "3f6c1e2a-7b8d-4c9e-a1f2-3b4c5d6e7f80",
      "overAllStatus": "hold",
      "createdBy": "string",
      "createdDate": "2026-09-15",
      "createdTime": "10:30 AM",
      "voucherNo": "string",
      "companyName": "string",
      "location": "string",
      "grnNo": "string",
      "debitCreditTo": "string",
      "payReceivedFrom": "string",
      "paymentMode": "string",
      "receiverName": "string",
      "amtWords": "string",
      "remark": "string",
      "voucherType": "multi-cash-voucher",
      "voucherTypeLabel": "Multi Cash Voucher"
    }
  ],
  "allRecords": 1,
  "totalPages": 1,
  "page": 1
}
```

**No matching records:** `200` with `data: []`, `allRecords: 0`.

**Errors:** `400` invalid filter or `voucherType` · `401` not authenticated · `500` unexpected error.

#### Export All Vouchers

|  |  |
|---|---|
| **Method** | GET |
| **Endpoint** | `/vouchers/export/excel` |
| **Purpose** | Downloads every voucher of every selected type matching the filters, in the same order as `GET /vouchers`; **no pagination**. |
| **Authentication** | Bearer token (or `access_token` cookie) |
| **Authorization** | Same row visibility as `GET /vouchers`. |
| **Path parameters** | None |

**Query parameters:** every `GET /vouchers` parameter; `page` and `limit` are ignored.

```bash
curl -X GET \
  "http://localhost:4000/vouchers/export/excel?startDate=2026-09-01&endDate=2026-09-15&voucherType=multi-cash-voucher,transport-payment-voucher&status=hold" \
  -H "Authorization: Bearer <token>" \
  --output All_Vouchers.xlsx
```

**Response `200`:** `All_Vouchers_<YYYY-MM-DD>.xlsx`, headers as in [§ 6](#6-export-file-format). The `All Vouchers` sheet has a `Voucher Type` column; the approval sheet is `Voucher Approvals`. Sheets:

| Sheet | Columns | Column headers (in order) |
|---|---|---|
| `All Vouchers` | 41 | Voucher Type, Voucher No, Voucher Record ID, Requesting Department, Company, Company GST No, Location, GRN No, Debit / Credit To, Pay / Received From, Amount, Payment Mode, Amount In Words, Receiver Name, Voucher Approval Status, Requested By, Requested By Employee ID, Passed By, Passed By Employee ID, Approved By (Voucher), Approved By (Voucher) Employee ID, Attachments, Remark, Created Date, Updated Date, Document ID, Overall Status, Last Approval Stage, Last Approval Action, Last Action By, Last Action Date, Approved By, Approval Date, Rejected By, Rejection Date, Rejection Reason, Document Remarks, Document Created By, Document Created By Employee ID, Document Created Date, Document Updated Date |
| `Multi Cash Voucher` | 41 | Voucher No, Voucher Record ID, Requesting Department, Company, Company GST No, Location, GRN No, Debit / Credit To, Pay / Received From, Delivery Challan No, Total Amount, Payment Mode, Amount In Words, Receiver Name, Voucher Approval Status, Requested By, Requested By Employee ID, Passed By, Passed By Employee ID, Approved By (Voucher), Approved By (Voucher) Employee ID, Attachments, Remark, Created Date, Updated Date, Document ID, Overall Status, Last Approval Stage, Last Approval Action, Last Action By, Last Action Date, Approved By, Approval Date, Rejected By, Rejection Date, Rejection Reason, Document Remarks, Document Created By, Document Created By Employee ID, Document Created Date, Document Updated Date |
| `MCV Particulars` | 7 | Voucher No, Voucher Record ID, Particular ID, Description, Amount, Created Date, Updated Date |
| `Labour Payment Voucher` | 47 | Voucher No, Voucher Record ID, Requesting Department, Company, Company GST No, Location, GRN No, Debit / Credit To, Pay / Received From, Loading Date, Products, No Of Labours, Rate Per Labour, Total Amount, Contact No, Alternate Contact No, KYC Done, Payment Mode, Amount In Words, Receiver Name, Voucher Approval Status, Requested By, Requested By Employee ID, Passed By, Passed By Employee ID, Approved By (Voucher), Approved By (Voucher) Employee ID, Attachments, Remark, Created Date, Updated Date, Document ID, Overall Status, Last Approval Stage, Last Approval Action, Last Action By, Last Action Date, Approved By, Approval Date, Rejected By, Rejection Date, Rejection Reason, Document Remarks, Document Created By, Document Created By Employee ID, Document Created Date, Document Updated Date |
| `Transport Payment Voucher` | 55 | Voucher No, Voucher Record ID, Requesting Department, Company, Company GST No, Location, GRN No, Debit / Credit To, Pay / Received From, Vehicle No, Driver Name, Contact No, Alternate Contact No, Dispatch Location, Destination Location, Products, Freight Amount, Decided Amount, Actual Amount, Advance Amount, Total Payable Amount, Deduction Amount, Extra Amount, Final Payable Amount, KYC Done, Payment Mode, Amount In Words, Receiver Name, Voucher Approval Status, Requested By, Requested By Employee ID, Passed By, Passed By Employee ID, Approved By (Voucher), Approved By (Voucher) Employee ID, Attachments, Remark, Created Date, Updated Date, Document ID, Overall Status, Last Approval Stage, Last Approval Action, Last Action By, Last Action Date, Approved By, Approval Date, Rejected By, Rejection Date, Rejection Reason, Document Remarks, Document Created By, Document Created By Employee ID, Document Created Date, Document Updated Date |
| `TPV Products` | 6 | Voucher No, Voucher Record ID, Product ID, Product Code, Product Name, Category |
| `Packing Material Voucher` | 46 | Voucher No, Voucher Record ID, Requesting Department, Company, Company GST No, Location, GRN No, Debit / Credit To, Pay / Received From, Seller Name, Seller Address, Contact No, Alternate Contact No, Purpose, Total Amount, KYC Done, Payment Mode, Amount In Words, Receiver Name, Voucher Approval Status, Requested By, Requested By Employee ID, Passed By, Passed By Employee ID, Approved By (Voucher), Approved By (Voucher) Employee ID, Attachments, Remark, Created Date, Updated Date, Document ID, Overall Status, Last Approval Stage, Last Approval Action, Last Action By, Last Action Date, Approved By, Approval Date, Rejected By, Rejection Date, Rejection Reason, Document Remarks, Document Created By, Document Created By Employee ID, Document Created Date, Document Updated Date |
| `PMPV Materials` | 10 | Voucher No, Voucher Record ID, Material ID, Item Name, Item Quantity, Item UOM, Rate, Amount, Created Date, Updated Date |
| `Voucher Approvals` | 12 | Voucher Type, Voucher No, Record ID, Document ID, Overall Status, Stage, Stage Order, Action, Action By, Action By User ID, Reason, Action Date |

**Errors:** `400` invalid filter or `voucherType` · `401` not authenticated · `404 {"status":"fail","message":"No records found for the given filters"}` · `500` unexpected error.

#### Import All Vouchers

Not available — no import endpoint exists for vouchers (see [§ 7](#7-import-apis)).
