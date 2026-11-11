# Architecture

[Back to FEEDO](../README.md)

## System boundaries

```mermaid
flowchart TD
    User[Household user] --> Mobile[Flutter mobile client]
    Mobile -->|Authenticated REST| Backend[Spring Boot backend]
    Backend --> Database[(PostgreSQL)]
    Backend --> Python[Stateless Python / FastAPI service]
    Python --> Ranking[Meal recommendation ranking]
    Python --> OCR[Receipt evidence extraction]
    OCR --> Hybrid[Hybrid mode when configured]
    OCR --> Legacy[Legacy OCR mode]
    Hybrid --> Paddle[PaddleOCR]
    Hybrid --> Tesseract[Tesseract]
    Legacy --> Tesseract
```

The mobile app presents household workflows. The backend authorizes access, validates requests, and owns durable business records. PostgreSQL persists those records. The Python service processes supplied recommendation context and receipt images without owning household persistence.

Docker and Docker Compose support service packaging and local orchestration. This diagram describes responsibilities, not a public deployment topology.

## Receipt processing pipeline

```mermaid
flowchart TD
    Image[User-selected receipt image] --> Upload[Backend accepts upload]
    Upload --> Queue[Background processing]
    Queue --> OCR[Python OCR and parsing]
    OCR --> Evidence[Structured receipt evidence]
    Evidence --> Suggestions[Catalogue suggestions and review reasons]
    Suggestions --> Review[User reviews lines and package mapping]
    Review --> Gate{Blocking issues resolved?}
    Gate -->|No| Review
    Gate -->|Yes| Confirm[Explicit confirmation]
    Confirm --> Transaction[Transactional purchase workflow]
    Transaction --> Purchase[Recorded purchase and spending]
    Transaction --> Inventory[Selected inventory additions]
    Queue -->|Processing failure| Failure[Failure state and retry handling]
```

Extraction does not itself create a purchase. Ambiguous financial evidence, unresolved food eligibility, incompatible units, and unmatched product rows can require user action. Review separates an OCR suggestion from an accepted household record.

Background work has ownership of an attempt and failure handling. Receipt-scoped confirmation checks distinguish a repeat request from a conflicting attempt, protecting purchase and inventory effects against duplicate confirmation.

## Conceptual data flow

```mermaid
flowchart LR
    Profile[Household profile and preferences] --> Plan[Meal plan]
    Stock[Current inventory] --> Plan
    Plan --> Shopping[Shopping preparation]
    Shopping --> Purchase[Recorded purchase]
    Receipt[Reviewed receipt] --> Purchase
    Purchase --> Budget[Budget and reports]
    Purchase --> Stock
    Stock --> Usage[Consumption and waste]
    Usage --> Reports[Household reports]
```

These are product relationships, not a database schema. A shopping estimate is not recorded spending. Receipt confirmation joins the ordinary purchase workflow so selected stock additions and financial recording follow consistent rules.

## Trust and privacy

The client communicates with the backend rather than directly modifying persistence. Authenticated ownership checks scope household access. OCR output is treated as uncertain input and validated before confirmation. Image handling belongs to the private application; no receipt photographs, service addresses, deployment secrets, or database structures are included here.
