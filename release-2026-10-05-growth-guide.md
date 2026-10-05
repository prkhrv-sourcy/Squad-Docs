# S-Quad release guide: Before & Now

5 October 2026 · Growth team

Upcoming release — awaiting release approval. “Now” means the new version.

## Creating a request

| What changes | Before | Now |
|---|---|---|
| Starting a request | Some options were hard to use. | Start manually, reorder, upload vendor files, or use agents. |
| Price & quantity | Invalid values could be saved. | Prices must be positive. Quantities must be positive whole numbers. |
| Vendor files | Processing could look stuck. Old uploads could trap you. | Status refreshes. Choose whether to resume an old upload. Switching modes keeps your form. |
| Product details | Some file details and written logo needs were missed. | More dimensions, weights and notes carry over. Written logo and engraving needs reach the agents. |

## Reordering

| What changes | Before | Now |
|---|---|---|
| Finding products | Many old requests showed nothing to copy. | Choose item briefs and previous products, including non-verified ones. |
| Keeping items together | One item and its options could become separate items. | Each item keeps its selected product options underneath it. Duplicate choices are removed. |
| Choosing what to copy | Product cards showed too much detail. | Compact cards make selection easier. Non-verified products are labelled. |
| Starting fresh | Supplier names and prices could leak into copied requirements. | Copied requirements leave those details out. Review quantities and validate products again. |

One food-container item → Supplier A’s product + Supplier B’s product + Supplier C’s product. Copy the options you choose.

Non-verified means the original product still needs checking. Copying it does not make it verified.

## Quotations & Google Sheets

| What changes | Before | Now |
|---|---|---|
| Quote history | A quotation count could appear with no quote shown. | Counts reflect saved quotes. Existing quote versions are visible. |
| Quote totals | The list total could differ from the quote document. | The list uses the published quote’s total. |
| Preparing early | You had to wait for product checks to draft a quote. | Use “Draft with current data” for a labelled preview. Validate products and rebuild before publishing. |
| Sheet prices | Some foreign-currency prices became zero. Missing prices could show errors. | New sheets convert prices correctly and leave missing prices without lookup errors. |
| Sheet progress | You had to reload to see when a sheet was ready. | Processing status refreshes automatically. |
| Delivery choices | Choices could stay outdated after changes. | Delivery choices refresh when relevant details change. |

Existing Google Sheets are not repaired automatically. The pricing fix applies to newly generated sheets.

## Agents & supplier work

| What changes | Before | Now |
|---|---|---|
| Choosing agents | Clicking “Use agents” could switch your chosen mode. | Your choice stays selected. The default is “Find products for me”. |
| Reorders & uploads | These flows offered only manual handling. | Hand the selected items to agents to find products or prepare a quote for review. |
| Knowing when to act | Finished searches could still look busy and show a quote date. | Search-only requests show “Products ready” and hand back to Growth, without a quote date. |
| Supplier follow-up | Selecting suppliers, questions and next steps was less guided. | The guided flow lets you choose products and questions, confirm outreach, and track replies. |

“Find products for me” stops at product search. “Prepare a quote for review” can contact suppliers.

## Requests that need your help

| What changes | Before | Now |
|---|---|---|
| Finding the problem | Blocker labels and agent history were harder to follow. | See what is blocked, which item needs help, and what the agents already tried. |
| Answering a supplier | You had to look elsewhere for the question. | The recorded question and context appear above your answer box. |
| Reading the brief | Requirements started hidden. | The brief starts open. Collapse it whenever you want. |
| Waiting for a customer | A resolved task could hide that you were still waiting. | The request continues to show that it is waiting for the customer. |

## Customer changes after a quotation

| What changes | Before | Now |
|---|---|---|
| Taking ownership | There was no clear way to claim the help request. | “Take over request” assigns it to you and shows who is handling it. |

Taking ownership does not pause agents or close the request. “Mark handled” closes the escalation with an internal note; it sends no customer message.

## Keep in mind

Quotation drafts still need Growth review before sending. The guided supplier flow must be enabled for your team.

Grouped customer changes and the “Tell customer: can’t do” action are held for a later release. WhatsApp import and specification-file uploads are also not included.

Source: [PR #291](https://github.com/Sourcy-Global/S-Quad/pull/291), excluding #289. Reviewed 5 October 2026 at 537486e. Contents may change before approval.
