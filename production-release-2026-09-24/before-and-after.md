# S-Quad production release — before and after

24 September 2026 · PR #235 · deployed commit `cfaa697`

Production deployment succeeded: https://github.com/Sourcy-Global/S-Quad/actions/runs/35943142581

Scope: changes since previous production commit `1f319b4`. Source-reviewed release behavior; not a fresh live-UAT sign-off.

## Build a quotation without losing your choices

Growth · quotation builder

| Change | Before | Now |
|---|---|---|
| Choose the origin warehouse | Growth could not explicitly change Sourcy’s origin/consolidation warehouse in the quotation builder. | Select the origin warehouse and use its available freight rates. This is Sourcy’s warehouse, not the customer’s delivery address. |
| Keep saved logistics visible during an outage | An unavailable logistics catalogue could make saved warehouse/rate selections disappear from the available choices. | Saved selections remain visible during catalogue outages. Rates from different origin warehouses are not mixed. |
| Keep terms and navigation stable | Overlapping saves could temporarily clear selected terms and move the wizard backwards. Permanent term loss was not reproduced. | Pending edits are reconciled with saved responses so older responses do not undo newer choices. |
| Enter sample inbound charges in CNY | Sample-price currency conversion already worked, but sample inbound charges still used a USD input flow. | Enter sample inbound fees in CNY; the application converts them into its internal USD calculation basis. The sample price keeps its own currency selection. |
| Recheck logistics after editing a request | A reset could clear selected routes but keep an old freight mode, allowing a built-in fallback estimate. | The old freight mode is cleared too. Review and select logistics again after changing the request. |

**What to do now:** Choose the correct origin before choosing freight. Review the sample price currency separately from the CNY inbound-fee field.

Evidence: [src/features/requests/quote-page.tsx](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/src/features/requests/quote-page.tsx), [src/queries/requests.ts](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/src/queries/requests.ts), [src/features/requests/components/sample-product-details.tsx](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/src/features/requests/components/sample-product-details.tsx), [server/service/quote.ts](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/server/service/quote.ts)

## Make delivery prices comparable

Growth · commercial review

| Change | Before | Now |
|---|---|---|
| Compare routes independently | Legacy route selections could be treated as combined legs rather than separate delivery offers. | Air, Sea and Land choices are priced independently. Each choice retains its own last-mile charge and external costs. |
| Match sea-freight calculation | Sea billing did not fully match S-Cube’s minimum, heavy-goods and volume-rounding rules. | Sea CBM rates use a minimum of 1 CBM, account for weight ÷ 500, and round billable volume upward to 0.1 CBM. |
| Apply markups to the intended costs | Product, inbound, outbound, QC and BNPL treatment differed from the S-Cube reference calculation. | Product markup covers goods + inbound. Logistics markup covers outbound freight. QC is added without logistics markup; eligible Philippines 60-day BNPL adds 3.62%. |
| Preserve small prices and currency totals | Premature unit or freight rounding could change the payable total, especially for large quantities of low-priced items. | Currency conversion and rounding follow the S-Cube calculation. Sub-cent unit prices are retained; payable amounts are rounded at the appropriate total level. |

**What to do now:** Compare each delivery option’s own total. “Margin” controls follow the implemented S-Cube markup calculation; they are not a cost ÷ (1 − margin) formula. QC is excluded for FOB and products-only quotes.

Evidence: [shared/quote.ts](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/shared/quote.ts), [shared/__tests__/quote-scube-parity.test.ts](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/shared/__tests__/quote-scube-parity.test.ts), [server/worksheets/sheet-pricing.ts](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/server/worksheets/sheet-pricing.ts)

## Keep the customer quotation clean

Growth · customer quotations and PDFs

| Change | Before | Now |
|---|---|---|
| Separate internal notes from customer copy | Internal sourcing summaries and escalation wording could reach customer-facing content. | Customer selling points are explicitly authored. Internal admin/escalation wording is filtered from supported customer text fields; the published document keeps only allowed fields. |
| Use the photo Growth approved | Source galleries could supply the displayed image, including evidence images. | The customer document uses Growth’s explicit customer-photo selection. Known screenshot/chat-image filenames are rejected. This is not automatic visual detection of every possible screenshot. |
| Hide supplier identity | Supplier names and marketplace IDs could remain in the published quotation data. | Customer options use neutral names and IDs such as Option 1. Marketplace product links and sourcing provenance are omitted; private source information remains available internally. |
| Compare the same customer requirements | Supplier-specific details could displace the shared requirements used to compare options. | Keep the shared customer requirements and show each supplier’s own answers or differences alongside them. |
| Retain commercial facts through publication | Reviewed fields could be lost or overwritten as quotation data was published or enriched. | Preserve reviewed content and commercial terms. The document displays each item’s quoted quantity and payable pricing, including unit-price precision. |

**What to do now:** Choose the customer photo, review selling points, check each quantity and total, and review the PDF before sending. A missing approved photo can leave the image blank.

Evidence: [server/quote/customer-document.ts](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/server/quote/customer-document.ts), [shared/quote-customer-content.ts](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/shared/quote-customer-content.ts), [shared/quotation-document.ts](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/shared/quotation-document.ts), [src/features/requests/published-quote-document.tsx](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/src/features/requests/published-quote-document.tsx)

## Publish once, then track delivery

Growth · customer notification

| Change | Before | Now |
|---|---|---|
| Publish and send together | Publishing made a quotation available, but the previous publish flow did not notify the customer. | Publish and send stores the reviewed quotation and asks Brain to send that exact published version. |
| Retry without making a new revision | Sending was not integrated into the publish flow with a retry tied to the saved version. | A failed send leaves the quotation intact. Retry sending the same version instead of publishing again. |
| Distinguish sent from unconfirmed | An acknowledgement or an uncertain response could be mistaken for completed delivery. | The UI distinguishes sent, pending and failed. Pending is not proof of delivery; its publish toast does not offer a retry shortcut. Check notification history first. |
| Preserve a successful send result | A cache-refresh error after a successful send could turn the request response into an error. | Cache invalidation failure is logged without replacing the delivery result. |

**What to do now:** If delivery fails, resend the saved quotation. If delivery is pending, check notification history before retrying. Publishing does not pause agents or transfer Brain ownership.

Evidence: [server/brain/commands.ts](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/server/brain/commands.ts), [server/routes/requests.ts](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/server/routes/requests.ts), [src/queries/requests.ts](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/src/queries/requests.ts), [docs/squad-brain-handoff.md](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/docs/squad-brain-handoff.md)

## Keep Initial, Sample and Bulk separate

Growth · quotation history and Google Sheets

| Change | Before | Now |
|---|---|---|
| Independent quotation workspaces | The application did not preserve fully independent drafts for Initial, every Sample round and Bulk. | Each stage/round keeps its own saved products, specifications, selection, prices and terms. |
| Start another sample round safely | A new sample iteration lacked its own preserved predecessor and independent publication history. | New sample round copies the latest saved sample round, preserves the old one and clears publication/final-settlement markers. Review the copied values before publishing. |
| Use the matching Google Sheet | A shared draft/sheet model could not safely isolate work across quotation rounds. | Each stage/round has its own Sheet. Automatic sync works on the active round; inactive-sheet edits are imported when that round becomes active again. |
| Protect against stale tabs and wrong-round writes | Concurrent operations could finish after the active quotation context changed. | Stale saves, imports and cross-round writes are rejected. Reload a stale tab rather than overwriting the current round. |
| Give Growth a visual Sheets guide | The team lacked the new consolidated visual guide for the worksheet flow. | A Growth Google Sheets guide is included in the release, alongside stage/round instructions and acceptance steps. |

**What to do now:** Sync Sheet edits before copying a round. Select the intended stage/round before editing, restoring history or generating an invoice. Existing sample data is treated as round 1.

Evidence: [docs/quote-stages-and-rounds.md](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/docs/quote-stages-and-rounds.md), [db/migrations/0046_quote_stage_rounds.sql](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/db/migrations/0046_quote_stage_rounds.sql), [server/worksheets/google.ts](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/server/worksheets/google.ts), [docs/guides/growth-google-sheets-guide.html](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/docs/guides/growth-google-sheets-guide.html)

## Make issued invoices stay accurate

Growth + Finance · invoices

| Change | Before | Now |
|---|---|---|
| Use the exact published quotation | Invoice pricing/selection did not consistently enforce the exact published quotation identity and pricing. | Create invoices from eligible published Sample/Bulk versions. Use that quotation’s ID, revision, quantities, prices and saved currency data—not the current draft. |
| Block incomplete pricing | Missing prices, quantities, exchange rates or unexplained totals could produce unreliable invoice output. | Incomplete or inconsistent published pricing blocks creation instead of silently becoming a zero-price invoice. |
| Choose billing and delivery details | Invoice rendering relied on fixed/default billing details rather than an invoice-specific saved snapshot. | Choose bill-to and ship-to addresses belonging to the customer. Review the payment profile matched to the published entity and currency. |
| Freeze the issued document | Later changes to customer details or configuration could affect how invoice information was rendered. | New invoices freeze seller, addresses, payment instructions and the priced document. Later edits do not rewrite that invoice; payment/void status can still change. |

**What to do now:** Check the selected round/revision, both addresses and the payment instructions before issuing. Older invoices are not retroactively given billing snapshots; legacy documents keep their compatibility behavior.

Evidence: [shared/invoice.ts](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/shared/invoice.ts), [server/service/invoice-billing.ts](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/server/service/invoice-billing.ts), [server/service/invoices.ts](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/server/service/invoices.ts), [docs/invoice-billing.md](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/docs/invoice-billing.md)

## Edit, close and repeat requests

Growth · sourcing requests (SRs)

| Change | Before | Now |
|---|---|---|
| Edit the full request | Squad lacked this full request-management editor. | Eligible requests can be edited for customer/contact, delivery address, owner, date, brief and item requirements, including quantities, target prices, specifications and reference images. |
| Keep list and detail views aligned | Shadow edit rows could leave old or removed item names in the list; products linked to edited specs could disappear from detail output. | The list uses the latest applicable edit/removal. Products attached to excluded edited-spec rows remain visible in the unassigned/extension product group. |
| Close requests with a reason | The new native close-as-lost action was unavailable. | Close eligible requests as lost with a recorded reason. Existing quotations and invoices remain available. |
| Copy or reorder without stale approvals | There was no equivalent native copy/reorder workflow for these product references. | Copy selected items from a request or past order into a new request or an eligible request for the same customer. Keep references, but clear old pricing and approvals for fresh review. |
| Respect the source and target round | Copying within the same SR could read source facts under the target round. | Source facts are read under the source round; destination changes remain scoped to the target. Manual reorders can be published without waiting for Brain to create a request. |

**What to do now:** Editing/closing remains blocked when Brain controls the request or ownership cannot be confirmed. Recheck stock, MOQ, prices and delivery on copied items.

Evidence: [src/features/requests/edit-request-page.tsx](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/src/features/requests/edit-request-page.tsx), [server/service/request-management.ts](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/server/service/request-management.ts), [server/legacy/requests.ts](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/server/legacy/requests.ts), [server/adapt/request.ts](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/server/adapt/request.ts)

## Understand why a request is blocked

Growth · escalation and agent activity

| Change | Before | Now |
|---|---|---|
| See progress per item | Escalation status was less explicit about which item was blocked and what type of blocker applied. | Show item-level progress, blocker kinds and the relevant option target in the brief/escalation view. |
| See which options counted | Growth had less visibility into qualification evidence behind a quotation-stage block. | Show qualification traces where Brain provides them: counted options, rejected candidates, duplicate variants and missing identity evidence. A trace-only escalation now renders its context. |
| Use accurate coverage labels | Requested-spec counts were labelled as must-have counts in some views. | Requested specs and mandatory specs are labelled separately, so a general coverage ratio is not presented as confirmed must-haves. |
| Do not guess evidence provenance | Missing provenance from older Brain responses was shown as a definite legacy-strategy fallback. | Missing/unrecognized provenance is unknown. Neutral wording replaces a claim that the UAS execution record was not retained. |

**What to do now:** Use the item-level evidence to decide the next action. Trace availability depends on Brain’s recorded data; the UI does not invent evidence for older runs or before quotation.

Evidence: [server/escalations/squad-routes.ts](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/server/escalations/squad-routes.ts), [server/adapt/request.ts](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/server/adapt/request.ts), [src/features/requests/components/agent-activity-dialog.tsx](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/src/features/requests/components/agent-activity-dialog.tsx), [src/features/requests/components/escalation-resolution-panel.tsx](https://github.com/Sourcy-Global/S-Quad/blob/cfaa6978703c08e8f8a2529bcc103cfdec5c4066/src/features/requests/components/escalation-resolution-panel.tsx)

## Boundaries

- Brain ownership handoff and agent pausing remain deferred; editing/closing Brain-controlled or unconfirmed requests is blocked.
- Old invoices are not backfilled with historical billing snapshots.
- Approved-photo selection and filename checks are not universal screenshot detection.
- Single/Carton validation, saved sample details, item quantities and payable-total display were also covered by earlier audits; do not present all of them as newly introduced here.
- The Google Sheet dimension correction was already on the previous production version.
- Production deployment was verified; live external Sheets/customer notification flows were not replayed for this guide.
- See the HTML/PDF for three workflow diagrams, the Growth checklist and rollout/validation details.
