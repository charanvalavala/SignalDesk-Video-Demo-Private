# HTTP API

All JSON mutations use `Content-Type: application/json`. Login returns an HttpOnly session cookie and a CSRF token. Send the token in `X-CSRF-Token` on every authenticated POST. Browser cross-origin mutations are rejected. GET endpoints require the session cookie except static assets. All error responses contain `error` and use a suitable HTTP status.

| Method | Path | Purpose / access |
|---|---|---|
| POST | `/api/register` | Public employee registration: name, email, password |
| POST | `/api/login` | Email/password login, cookie and CSRF token |
| GET | `/api/me` | Current user and CSRF token |
| POST | `/api/logout` | Revoke current session |
| GET | `/api/meta` | Categories, services, strategy; technician data for staff |
| GET | `/api/incidents` | Role-scoped incident register |
| POST | `/api/incidents` | Create and automatically triage a report |
| GET | `/api/incidents/{id}` | Authorized details, notes, audit and resolution suggestions |
| POST | `/api/incidents/{id}/transition` | Guarded status change; status, optional version, resolution when resolving |
| POST | `/api/incidents/{id}/assign` | Administrator; technician_id or automatic strategy |
| POST | `/api/incidents/{id}/note` | Authorized participant; body, kind |
| POST | `/api/incidents/{id}/update` | Assigned technician/admin; impact, urgency, affected_users |
| POST | `/api/incidents/{id}/feedback` | Reporter after resolution; rating 1-5, optional text |
| GET | `/api/report` | Role-scoped overview, service counts; admin workload |
| GET | `/api/export` | Role-scoped filtered CSV |
| GET | `/api/notifications` | Current user's two-channel inbox |
| POST | `/api/notifications/read` | Mark current user's alerts as read |
| GET | `/api/admin` | Administrator; users, SLA policies and global audit |
| POST | `/api/users` | Administrator creates a user |
| POST | `/api/config` | Administrator manages strategy, category, service, SLA or user |
| POST | `/api/monitor` | Administrator manually triggers the SLA scan |

Incident filters: `q`, `status`, `priority`, `category`, `service_id`, `technician_id`, `sla_status`, `from`, `to`. Dates use YYYY-MM-DD and filter UTC creation dates inclusively. Text matches incident ID, title and description. Filters combine with AND.

Creation payload:

```json
{
  "title": "Payment application unavailable",
  "description": "Checkout transactions fail for the entire sales department.",
  "service_id": 1,
  "category": "Application",
  "impact": 3,
  "urgency": 3,
  "affected_users": 150
}
```

Leave category empty for a keyword suggestion. A possible duplicate is retained and linked; creation is not rejected. If no eligible technician exists, the incident remains CLASSIFIED and an administrator notification is recorded.

Status codes: 400 invalid input, 401 login required or invalid credentials, 403 unauthorized or CSRF failure, 404 missing resource, 409 business-rule conflict, 413 oversized body, 415 incorrect content type, 429 login throttle, 503 database unavailable, 500 unexpected server error. Internal tracebacks stay in the server log.
