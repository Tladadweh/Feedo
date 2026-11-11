# Product features

[Back to FEEDO](../README.md)

## Set up the household

Create an account and maintain a household profile, members, food preferences, and budget. Household context helps tailor meal planning. Household records are scoped through the authenticated account; this description does not imply shared-account invitations or a collaborative multi-login feature.

## Decide what to cook

Browse the meal catalogue and inspect recipes. Generate a plan using household context, preferences, and a planning budget. Review suggested meals and their reasons, adjust meals or servings, then confirm the plan. Recommendation feedback lets users respond to suggestions.

The implemented recommendation path includes ranking that works without a trained household history model. Learned personalization is not claimed as complete.

## Prepare the shopping

Maintain grocery lists with product quantities and estimated costs, update entries, and review list history. Planning estimates help prepare a shop; a list estimate is separate from actual purchase spending.

## Record purchases and spending

Record purchased products and amounts, review purchase history, and compare actual spending with a household budget. Reports summarize recorded activity, including spending and waste. Planning estimates and recorded financial totals serve different purposes.

## Know what is at home

Track stock quantities, storage locations, and best-before dates. Record consumption, additions, removals, and discarded food. Inventory and waste views help users understand what remains and what was lost. Dates are recorded information; this showcase makes no claim of AI expiry prediction.

## Turn receipts into reviewed records

1. Photograph or select a receipt image for upload.
2. Let background processing extract receipt evidence and suggest catalogue matches.
3. Review detected lines, food eligibility, product choices, quantities, units, and final amounts.
4. Resolve blocking uncertainty or explicitly ignore lines that should not be imported. Non-food rows are excluded from food inventory.
5. Review package mapping when pack counts and canonical inventory units differ.
6. Confirm the purchase and choose which accepted items enter inventory.

Hybrid OCR combines PaddleOCR and Tesseract when configured and provisioned. OCR suggestions can be incomplete or incorrect. Confirmation requires review safeguards, and retry handling prevents the same receipt from creating another purchase through repeated confirmation.

## Stay oriented

In-app notifications surface supported events such as meal-plan and recommendation readiness. English and Arabic interface resources and locale selection are implemented; coverage is not uniformly verified across every screen or catalogue entry. Automatic low-stock reminders are not established by the demo screenshots.

## Preview context

The [gallery](../README.md#screenshots) uses fictional household data in the real app. See the [screenshot notes](../assets/screenshots/README.md) for financial-label and receipt-demo limitations.
