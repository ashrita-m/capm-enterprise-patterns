# CAPM Enterprise Patterns

This repository documents **sanitized, reusable patterns** I used while building an enterprise “held orders”-style application on SAP BTP using CAPM.

## What’s inside

- **docs/architecture.md**  
  A sanitized reference architecture (ERP + external APIs + data lake → integration → cloud DB → CAP service → secure UI).

- **docs/patterns.md**  
  Practical patterns for:
  - server-side pagination / filtering / sorting
  - CAP service handlers (before/after READ)
  - app-only data storage (comments, interim flags)
  - reliability + error-handling approach (integration layer)

- **docs/troubleshooting.md**  
  Common issues and how to debug them 

---

## Tech Themes Covered

- CAP service design (CDS modeling + service layer)
- Cloud DB persistence approach (app-only vs system-of-record)
- Integration orchestration concepts (multi-source aggregation)
- Authentication/authorization concepts (role-based access)

---


