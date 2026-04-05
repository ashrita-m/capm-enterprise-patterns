# Glossary (CAPM & SAP BTP – Simplified)

This file captures the main technical concepts used in this project, explained in a simple and practical way based on how they were actually used during development.

## CAPM (Cloud Application Programming Model)
- CAPM is SAP’s framework for building backend applications on BTP.
- It helps structure the application clearly by separating data modeling, service exposure, and business logic.
- In this project, CAPM acts as the core backend layer between the database and the UI.

## CDS (Core Data Services)
- CDS is used to define the application’s data model.
- It allows us to describe entities, relationships, projections, and service definitions in a clean and declarative way.
- In simple terms, CDS defines what the data looks like and how it should be exposed.


## Entity
- An entity represents a structured dataset, similar to a table in the database.
- Examples in this type of project include orders, payer codes, statuses, and additional order-related data.
- Entities usually hold the main persisted business data.


## Projection
- A projection is a controlled view of an entity.
- Instead of exposing the full database structure directly, projections allow us to expose only what the service or UI needs.
- They help keep the API clean and separate the database model from the service design.


## Virtual Field
- A virtual field is not physically stored in the database.
- It is calculated dynamically in the service layer at runtime.
- In this project, virtual fields were useful for values such as flags or indicators that depend on business logic.


## CAPM Service Layer
- The service layer is where the backend APIs are exposed to the UI.
- It is responsible for handling requests, applying business logic, enforcing authorization, and interacting with the database.
- In this project, the service layer acts as the main control point for how data flows to the front end.


## OData
- OData is the protocol used for communication between the UI and backend.
- It supports query options such as:
  - `$top` for limiting records
  - `$skip` for pagination
  - `$filter` for filtering
  - `$orderby` for sorting
- One of the advantages of CAPM is that it can translate these query options into efficient database queries automatically.


## before / after / on (CAPM Handlers)
- These are hooks in CAPM used to add custom logic at different stages of request processing.

### before
- Runs before the main database operation.
- Commonly used for validation, authorization checks, or modifying the request.

### after
- Runs after data is fetched or processed.
- Useful for enrichment, computed values, or formatting the response.

### on
- Overrides the default behavior completely.
- Used when custom processing is needed, such as external integrations or virtual entities.

## HANA Cloud
- HANA Cloud is the database used in SAP BTP.
- In this project, it stores both replicated backend data and application-specific data.
- It acts as the main persistence layer for the application.

## Integration Layer
- The integration layer is responsible for connecting different systems.
- It handles orchestration, transformation, and movement of data between sources.
- In this project, it helped connect ERP data, external APIs, and other sources before that data reached CAPM.

## Datasphere
- Datasphere is used to model and combine data from multiple sources.
- It helps prepare structured datasets for downstream consumption.
- In this project, it plays a role in organizing and exposing external or analytical data.


## System of Record
- The system of record is the source that owns the official version of business data.
- In this project, ERP remains the system of record.
- The BTP application does not replace that ownership; it works alongside it.

## App-Only Data
- App-only data refers to values that are needed by the application but do not belong in ERP.
- Examples include comments, UI-specific flags, or temporary workflow states.
- This type of data is stored in HANA Cloud to keep the ERP system clean and avoid unnecessary coupling.

## Role-Based Access Control (RBAC)
- RBAC is the approach used to control who can see or change what.
- In CAPM, this can be enforced using roles, user attributes, and CDS annotations like `@restrict`.
- This helps ensure that users only access data relevant to their role.

## Association
- An association defines a relationship between two entities.
- For example, one order can be linked to additional order data or related inbound deliveries.
- Associations help model connected data without duplicating it unnecessarily.


## N+1 Query Problem
- This is a common performance issue where one query fetches a list of records, but then additional queries are executed for each individual record.
- It increases database round-trips and slows down the application.
- A better approach is to use batching or grouped retrieval wherever possible.


## Separation of Concerns
- This means splitting the application into layers with clear responsibilities.
- In this project, that generally means:
  - Data layer for persistence
  - Service layer for business logic
  - Integration layer for orchestration
  - UI layer for presentation
- This makes the system easier to understand, maintain, and scale.
