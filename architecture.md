# Reference Architecture (Sanitized)

## Goal
Build a cloud-native “held orders” style application that aggregates data from an ERP system and external sources, and provides a secure, performant UI via a CAP-based service layer.

## High-Level Flow
1. ERP exposes operational data via OData APIs.
2. External systems provide supplementary data via REST APIs.
3. A data lake provides analytical/reference datasets (e.g., promotions).
4. Integration layer orchestrates extraction/transform and pushes data to a cloud database.
5. CAP service exposes domain APIs (OData/REST) to the UI and applies business rules.
6. Identity provider secures user access (roles/scopes).
7. App-specific data (comments, interim flags) is stored in the cloud DB and not written back unless required.

## Key Design Decisions
- Keep ERP as system of record; avoid unnecessary write-backs.
- Store “app-only” fields in the cloud DB to reduce coupling.
- Enforce server-side filtering/sorting/paging for large datasets.
- Use centralized integration/error handling for operational reliability.
