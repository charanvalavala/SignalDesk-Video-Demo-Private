# Operational Workflows

## Workflow 1: incident reporting and automatic triage

**Actors:** Employee, system and technician.

1. The employee signs in and opens Report incident.
2. The employee enters the title, description, affected service, impact, urgency and affected-user count. Category may be selected explicitly or suggested from the description.
3. The system calculates priority, selects the service-specific SLA and stores response and resolution deadlines.
4. The configured assignment strategy selects an available technician with spare capacity. If no candidate qualifies, the incident remains CLASSIFIED for administrator assignment.
5. The system records creation, classification and assignment in the audit trail and creates in-app and simulated email notifications.
6. The employee views the incident and its status in My incidents.

**Example:** Payment Gateway, impact 3, urgency 3 and 150 affected users produce score 26 and P1 priority. The default response target is five minutes and the resolution target is thirty minutes.

## Workflow 2: investigation, resolution and verified closure

**Actors:** Assigned technician and reporting employee.

1. The technician opens the incident in My queue and acknowledges it. The acknowledgement timestamp ends response-time measurement.
2. The technician starts investigation, adds investigation notes and requests additional information when needed.
3. The employee supplies additional information through the incident conversation.
4. The technician records the corrective action and validation results, then marks the incident RESOLVED. The resolution timestamp ends resolution-time measurement.
5. The employee reviews the resolution, verifies the outcome and closes the incident.
6. The employee provides a rating and feedback. Notifications and audit records preserve the complete sequence.

**Example resolution:** Restarted the checkout worker, cleared a stale lease and verified successful payment transactions. A worker health check was added to detect recurrence.

**Business controls:** Invalid state transitions are rejected. Resolution requires meaningful details. Only the reporter or an administrator may verify and close. The assigned technician or administrator can reopen a resolved incident for further investigation.

## Workflow 3: duplicate detection and SLA escalation

**Actors:** Employee, system and administrator.

1. An employee reports a payment failure while related reports remain open for the same service.
2. The system compares token sets and identifies possible duplicates at a Jaccard similarity of at least 0.30. The new report is retained and linked to the strongest candidate.
3. Staff may view related historical resolutions when a similar resolved case exists.
4. The background SLA monitor checks response and resolution targets every fifteen seconds. An unfinished target generates a warning after 80% of its interval.
5. A missed target generates a persisted breach event. An unacknowledged P1 incident beyond its response deadline also generates administrator escalation.
6. The administrator examines the overview, notification center and audit log, then reviews assignment or service policy as required.

**Business controls:** Monitor events are unique per incident and event type. Repeated scans do not duplicate alerts. Similarity identifies candidates and does not automatically merge or delete reports.

## Reporting and configuration

The administrator can switch between skill-based and load-based routing, manage technician skills and availability, add services and categories, and configure per-service SLA policies. The incident register supports text, status, priority, category, service, technician, SLA state and inclusive creation-date filters. Filtered results can be exported as CSV.
