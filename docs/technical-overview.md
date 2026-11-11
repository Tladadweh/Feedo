# Technical overview

[Back to FEEDO](../README.md)

## Flutter mobile client

The Dart/Flutter app groups screens by household workflow. Views present data and actions; controllers manage screen state; repositories handle backend communication through a shared REST client. Provider supports state wiring, Dio handles HTTP, and secure storage supports session persistence.

Loading, processing, review, and error states matter especially for receipt uploads: a successful upload is not the same as successful recognition or a confirmed purchase.

## Spring Boot backend

Java 21 and Spring Boot provide REST services, validation, security, and business orchestration. Spring Security and token-based authentication support account access. Household ownership is resolved from authenticated context and checked when accessing household resources.

The backend owns monetary and inventory changes. Keeping these rules in one place reduces drift between manual purchases and purchases created from reviewed receipts.

## PostgreSQL persistence

PostgreSQL stores household profiles, planning activity, lists, purchases, stock, waste, and receipt lifecycle records. Flyway manages schema evolution privately. Transaction boundaries keep accepted purchase and inventory effects consistent; generated extraction suggestions are stored separately from final business effects.

## Python recommendations and OCR

FastAPI exposes stateless processing services to the backend. Meal ranking works with supplied household and candidate context, including preferences and cost information. The current implementation includes a cold-start recommendation path; it does not justify claiming a completed learned household model.

Receipt extraction has legacy and hybrid modes. The hybrid path uses PaddleOCR and Tesseract, with runtime/model readiness requirements. Arabic and mixed-script receipts introduce text-order, recognition, quantity, and amount ambiguity. The public documentation intentionally stays at the workflow level rather than exposing parsing or ranking algorithms.

## Receipt lifecycle and asynchronous work

Upload acceptance creates work for a backend worker. Recognition runs outside the upload request, and the backend persists the resulting structured evidence and catalogue suggestions. The client can retrieve processing status and present review or failure states. Attempt handling and retries support recovery from interrupted processing.

Review can address food/non-food eligibility, catalogue choices, quantities, package-to-canonical-unit mapping, and payable amounts. Unknown evidence remains distinguishable from confirmed values. Unresolved blocking issues prevent confirmation.

## Consistency and retry safety

Receipt confirmation uses the ordinary purchase service inside a transaction. Confirmed purchase lines drive budget reconciliation and selected inventory additions. Receipt-scoped idempotency recognizes an equivalent repeated confirmation; conflicting confirmation details do not silently create a second purchase. These safeguards address the business effects of retries, rather than assuming network calls happen only once.

Package mapping is a distinct review concern: a purchased package count may represent liters, kilograms, or individual pieces in inventory. Users must resolve incompatible or uncertain mappings before affected lines can be safely accepted.

## Localization and notifications

The app contains English/Arabic locale selection and translated interface resources. Full localization of every label and catalogue entry has not been verified. In-app notifications support implemented workflow events; the screenshot set is not evidence that every possible automatic reminder exists.

## Engineering evidence and limits

This showcase was prepared from read-only inspection of the current local production branches of the three application components. It describes implemented workflows, not a new runtime acceptance test or security certification. The beta version metadata and current workflows support describing FEEDO as under development; no public live deployment is asserted.

The English product captures were prepared separately using fictional demo data. Their confirmed receipt records illustrate the UI, not a new OCR benchmark. See [screenshot context](../assets/screenshots/README.md).
