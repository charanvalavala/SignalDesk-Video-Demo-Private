# Design Rationale

## Language selection

The domain has clear contracts, interchangeable policies and transactional application services. Python's ABCs, dataclasses, exceptions, dictionaries and sets express these directly. Its standard library keeps the delivered application easy to run. The language choice is about reproducibility and design clarity.

## Frontend selection

The application has a small number of workspaces. Plain JavaScript, HTML and CSS support accessible forms and responsive layouts without a build pipeline. A larger client would benefit from typed components and a router.

## Object-oriented structure

`PriorityPolicy`, `AssignmentStrategy` and `NotificationChannel` define abstractions. Concrete strategies and channels inherit these contracts. `IncidentService` receives a repository, clock, priority policy and channels through its constructor. `User` is an immutable value object that validates role privileges. `IncidentLifecycle` protects the status graph.

## Account roles and inheritance

They are access roles attached to the same account structure. A role field avoids redundant subclasses with little distinct behavior. Inheritance is used where there is a real interchangeable implementation of a contract: a skill-based strategy is an assignment strategy.

## Design patterns

Strategy for two assignment policies and the replaceable priority policy; Repository/Unit of Work for SQLite persistence and transaction boundaries. Notifications use polymorphic channel objects composed into the service. The lifecycle is a transition-table state machine, not a collection of State-pattern subclasses. Event publishing is synchronous, not a full Observer bus.

## Encapsulation

Mutating operations are exposed through the service; HTTP callers cannot directly set arbitrary fields or SQL. The state machine rejects invalid transitions. Password hashes never leave account storage. Python's underscore helpers signal internal operations; they are not a security boundary. Server-side authorization and SQL constraints are the actual enforcement points.

## Priority calculation

Score = 3I + 3U + 2C + A. I, U and C range 1-3. A = 0 below 20 affected users, 1 for 20-99, and 2 for 100 or more. P1 begins at 23, P2 at 18, P3 at 12, and P4 is below 12. The maximum is 26. This makes impact and urgency strongest while service criticality and population adjust the outcome.

## Assignment policies

Skill-based routing requires category expertise, then ranks experience, load and ID. Load-based routing minimizes active-load/capacity ratio, then prefers skill match, experience and ID. It may assign a generalist. Both exclude unavailable, inactive and full technicians. A serialized transaction prevents two simultaneous assignments from each seeing the same last capacity slot.

## SLA measurement

Each service has response and resolution targets for each priority. Deadlines are creation time plus the selected targets. Response ends at acknowledgement; resolution ends at resolution. Timely completion stays MET as time passes; late completion stays BREACHED. Reopening clears the resolution time and retains the original deadline. Warnings and breaches are keyed by incident and event type, so repeated scans do not create duplicate alerts.

## Automated incident analysis

Keyword category suggestions, token-set duplicate similarity, historical resolution recommendations and automated P1 escalation turn incident data into actions. These are explainable rules, not a trained model. Duplicate matches require the same service and Jaccard similarity of at least 0.30. They are suggestions, never automatic deletion or merging. Candidate search is capped at the most recent 300 relevant incidents.

## Limitations

Token overlap misses paraphrases and may overmatch generic language. Generalist load routing needs managerial judgment for specialist incidents. SLA measurement is elapsed clock time, with no business calendar or waiting pause. The monitor operates only while the server is running. The prototype is a single-process local service with SQLite, not a performance-tested enterprise deployment. Read paths also use serialized repository transactions, favoring simplicity over throughput.

## Security controls

PBKDF2-SHA256 with a random 16-byte salt and 260,000 iterations; constant-time digest comparison; random sessions stored as hashes; 8-hour session expiry; HttpOnly and SameSite=Strict cookies; CSRF header validation; role and incident ownership checks; parameterized SQL; input bounds; escaped text rendering; CSP; login throttling; spreadsheet formula protection in CSV. Secure cookies require HTTPS and are a deployment follow-up.

## Scalability

Replace the HTTP adapter with a production server, put HTTPS at the edge, move persistence to PostgreSQL, add pagination and targeted SQL reads, run SLA work in a durable job queue, and separate notification delivery into a retrying outbox worker. Add migrations, account recovery, tenant boundaries, structured observability and measured capacity tests. The domain strategies and service contracts can remain.

## Failure handling

A transaction rolls back partial incident, audit and notification writes. No technician leaves the incident CLASSIFIED for manual recovery. Missing SLA blocks creation with a clear conflict. Database errors return 503 and remain in logs. A lost browser connection shows a retry message. Repeated create requests are not idempotent; users should check their register before resubmitting after an uncertain response.

## Validation

Forty automated cases use temporary SQLite files and a controllable clock. Browser checks exercise all three roles, the complete resolution workflow, duplicate suggestions, notifications, filtering, CSV and a 390px viewport. Tests establish the demonstrated behavior, not a guarantee of production security or scale.
