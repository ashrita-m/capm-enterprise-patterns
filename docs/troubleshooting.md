Troubleshooting & Learnings (Sanitized)

This document captures some of the key challenges I ran into while building a CAPM-based enterprise application, along with how I approached and resolved them. Most of these learnings came from practical implementation, debugging, and refining the design as the project evolved.

1) Pagination Needed to Move to the Backend

One of the early issues I ran into was around pagination. At first, the UI was trying to handle too much of it, which quickly became inefficient once the dataset started growing. It led to larger payloads, slower rendering, and a less consistent experience overall.

After spending time understanding how CAPM handles OData queries, I moved the pagination logic to the service layer and relied on query options like `$top` and `$skip`. That made a big difference because the database only returned the subset of records that was actually needed.

What I learned: 
For enterprise-scale datasets, pagination should be handled at the backend, not in the UI.


2) Filtering Had to Be Pushed Down Earlier

Another problem was filtering. In the beginning, some filtering behavior was effectively happening after too much data had already been retrieved. That meant extra records were being pulled unnecessarily and then reduced later, which was not a good use of the database or the service layer.

Once I aligned the filtering with CAPM request handling, the service could push those conditions down to the database properly. That improved both performance and clarity.

What I learned:
Filtering should happen as early as possible in the request flow, ideally at the query level.


3) Handling Derived Fields Without Polluting the Database

There were a few fields in the application that were needed by the UI but did not belong in the physical database schema. Examples included things like promotion indicators or editability flags, which depended on runtime logic rather than stored data.

Instead of storing those values permanently, I defined them as virtual fields and populated them in the service layer after the read operation. That kept the persistence model clean while still giving the UI everything it needed.

What I learned: 
Not every field belongs in the database. If something is dynamic or context-based, the service layer is usually the better place for it.

4) Separating ERP Data from App-Specific Data

A very important design decision in the project was around where to store application-specific fields such as comments, review flags, and temporary statuses. These values were important for the app, but they did not belong in the ERP system itself.

The solution was to maintain a separate cloud-side table for application-managed data and connect it to the main ERP-derived dataset through associations. This kept the ERP system clean while still allowing the application to support richer business workflows.

What I learned:
ERP should remain the system of record, while BTP can manage app-specific state and flexibility.

5) External Service Failures Needed Better Handling

In some cases, the application needed to call an external service to update backend information. Naturally, those calls did not always succeed. If handled poorly, those failures could block the user flow completely or leave the application in an awkward state.

To improve this, I treated some integration failures as warnings instead of immediate hard stops, depending on the situation. I also added logging and fallback behavior so that failures could still be traced and understood later.

What I learned: 
Not every failure should be treated the same way. It is important to distinguish between critical errors and recoverable integration issues.

 6) Repeated Queries Became a Performance Concern

As the logic became more complex, I noticed places where repeated database calls were happening inside loops. That pattern works initially, but it becomes expensive very quickly once the number of records increases.

I refactored that logic to batch lookups wherever possible and reduced unnecessary round-trips to the database. That improved performance and also made the service logic cleaner.

What I learned:
Even if the logic is functionally correct, repeated database access patterns can become a hidden performance bottleneck.


7) Authorization Could Not Rely Only on the UI

In the early stages, some of the access control assumptions were too UI-driven. That is risky because UI restrictions alone are never enough for real security.

I moved the responsibility into the service layer using CDS-based authorization and user attributes. That way, even if a request bypassed the UI, the service still enforced what the user was allowed to read or modify.

What I learned:
Authorization should always be enforced at the service level, not just at the presentation layer.

 8) Mixed CREATE and UPDATE Behavior Needed Careful Handling

One of the trickier areas in the project was dealing with UI behavior that triggered both CREATE and UPDATE type flows in similar scenarios. That introduced complexity in the backend because I had to make sure the logic remained consistent regardless of how the request arrived.

To handle that, I added logic to normalize certain cases and also introduced safeguards to avoid unwanted recursion or duplicate processing.

What I learned:
Understanding how the UI actually behaves is just as important as writing backend logic. The backend has to be aligned with real request patterns, not ideal assumptions.

9) Enriching Data from Multiple Sources Required Clear Boundaries

The application was not working with a single source of truth. It needed to combine information coming from ERP, external systems, and other data sources. That made enrichment useful, but also easy to overcomplicate.

What helped was keeping the boundaries clear:
- the integration layer handled orchestration,
- the database handled persistence,
- and the CAPM service handled runtime enrichment for the UI.

That separation made the application easier to reason about and maintain.

What I learned:
The more systems involved, the more important separation of concerns becomes.

10) End-to-End Debugging Required Layer-by-Layer Thinking

One of the biggest practical lessons from the project was around debugging. When something went wrong, it was rarely enough to look at only one place. Sometimes the issue was in the UI request, sometimes in CAPM logic, sometimes in data replication, and sometimes in integration.

The only reliable approach was to debug step by step:
- verify the UI request,
- check service handling,
- inspect the data in persistence,
- and then trace integration behavior if needed.

That approach saved a lot of time compared to guessing.

What I learned:
In distributed applications, debugging has to be systematic. Tracing one layer at a time is far more effective than jumping around.

Final Takeaway

The biggest lessons from this project did not come from simply using CAPM or SAP BTP features. They came from understanding where logic belongs, how data should flow across layers, and how to keep the design maintainable as complexity grows.

A lot of the real improvement came from correcting things like:
- logic being placed in the wrong layer,
- overly UI-dependent behavior,
- unnecessary coupling with external systems,
- and avoidable database inefficiencies.

Working through those challenges helped shape a much cleaner and more scalable architecture.
