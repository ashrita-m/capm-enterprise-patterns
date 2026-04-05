CAPM Enterprise Patterns

This document captures the key design patterns and practical approaches I applied while building an enterprise-grade CAPM application on SAP BTP. These patterns are derived from real implementation challenges and are written to be reusable across similar projects.

1) Server-Side Pagination Pattern
Problem

In enterprise scenarios, datasets such as orders or line items can grow very large. Loading full datasets into the UI leads to performance issues, increased memory usage, and poor user experience.

Solution

Pagination is handled at the CAP service layer using OData query options:

$top
$skip
$orderby
$filter

CAPM translates these automatically into optimized database queries, ensuring that only the required subset of data is fetched.

Key Insight

Pagination should always be handled at the service/database level.
Client-side pagination is not scalable for large datasets.

2) Server-Side Filtering Pattern
Problem

Applying filters on the UI after loading data leads to unnecessary data transfer and inefficient processing.

Solution

Filters are passed from the UI to CAPM and pushed down to the database layer:

UI → CAPM → SQL WHERE clause

This ensures that only relevant data is retrieved.

Key Insight

Filtering must happen before data retrieval, not after.
This is critical for performance and scalability.

3) CAPM Handler Pattern (before vs after READ)
before READ

Used to:

validate incoming requests
enforce authorization rules
restrict dataset based on user context
after READ

Used to:

enrich response data
compute derived fields
prepare UI-friendly structures
Key Insight
before handlers control what data is fetched
after handlers control how data is presented

This separation keeps logic clean and maintainable.

4) Virtual Field Pattern
Problem

Some fields are dynamic (e.g., flags, computed values) and should not be persisted in the database.

Solution

Use virtual fields in CDS and populate them in the service layer (after READ).

Examples:

Promotion indicator (Promo)
Edit permissions (isEditable)
Key Insight

Keep the database schema clean.
Dynamic and computed values should be handled in the service layer.

5) App-Only Data Storage Pattern
Problem

Certain data does not belong in ERP systems:

user comments
UI-driven flags
temporary processing states
Solution

Store such data in the cloud database (HANA on BTP) instead of pushing it back to ERP.

Key Insight
ERP remains the system of record
BTP handles application-specific state and flexibility

This reduces coupling and improves maintainability.

6) External Integration Pattern
Problem

Enterprise applications often require data from multiple sources:

ERP systems
external APIs
data lakes
Solution

Use an integration layer to:

orchestrate data flow
transform payloads
centralize error handling
Key Insight

CAPM should not directly integrate with multiple systems.
An integration layer ensures better separation, reliability, and scalability.

7) Role-Based Authorization Pattern
Problem

Different users require different levels of access (read-only vs editable data).

Solution

Use CDS-based authorization with @restrict and user attributes:

View roles → limited read access
Edit roles → read/write access
Key Insight

Authorization must be enforced at the service layer, not just the UI.
This ensures consistent and secure access control.

8) Read Optimization Pattern
Problem

Executing queries repeatedly (row-by-row or per entity) leads to performance bottlenecks.

Solution
Batch database queries
Use IN clauses for grouped lookups
Avoid N+1 query patterns
Key Insight

Minimize database round-trips.
Efficient query design is critical for scalable applications.

9) Error Handling Pattern
Problem

External dependencies (e.g., ERP calls) may fail, but not all failures should block user operations.

Solution
Use warnings for non-critical failures
Log errors for traceability
Allow controlled fallback behavior
Key Insight

Differentiate between:

critical failures (must stop execution)
recoverable issues (can continue with warnings)
10) Separation of Concerns Pattern
Architecture Layers
Data Layer → HANA / persistence
Service Layer → CAPM business logic
Integration Layer → data orchestration
UI Layer → presentation and interaction
Key Insight

Each layer should have a clearly defined responsibility.
This separation improves scalability, maintainability, and debugging.
