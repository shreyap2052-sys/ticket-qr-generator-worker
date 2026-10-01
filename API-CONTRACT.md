# API Contract — Ticket QR Code Generator Worker

## Base URL

```text
/api
```

All API responses use a consistent JSON structure.

---

## Standard Response Structure

### Success

```json
{
  "success": true,
  "data": {},
  "meta": {}
}
```

### Error

```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable error message",
    "fields": {}
  }
}
```

---

# 1. Get Tickets

## `GET /api/tickets`

Returns the available tickets.

### Success — Tickets Found

**Status:** `200 OK`

```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "ticketNumber": "TKT-1001",
      "customerName": "Demo Customer",
      "status": "active",
      "createdBy": "user-uuid",
      "createdAt": "2026-10-01T10:00:00Z",
      "updatedAt": "2026-10-01T10:00:00Z"
    }
  ],
  "meta": {
    "total": 1
  }
}
```

### Success — Empty

**Status:** `200 OK`

```json
{
  "success": true,
  "data": [],
  "meta": {
    "total": 0
  }
}
```

The frontend must display a user-friendly empty state such as:

```text
No data found.
```

A blank screen must never be used for an empty collection.

### Server Error

**Status:** `500 Internal Server Error`

```json
{
  "success": false,
  "error": {
    "code": "SERVER_ERROR",
    "message": "Unable to retrieve tickets.",
    "fields": {}
  }
}
```

---

# 2. Get Single Ticket

## `GET /api/tickets/:id`

Returns a single ticket.

### Success

**Status:** `200 OK`

```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "ticketNumber": "TKT-1001",
    "customerName": "Demo Customer",
    "status": "active",
    "createdBy": "user-uuid",
    "createdAt": "2026-10-01T10:00:00Z",
    "updatedAt": "2026-10-01T10:00:00Z"
  }
}
```

### Ticket Not Found

**Status:** `404 Not Found`

```json
{
  "success": false,
  "error": {
    "code": "TICKET_NOT_FOUND",
    "message": "Ticket not found.",
    "fields": {}
  }
}
```

---

# 3. Create Ticket

## `POST /api/tickets`

Creates a new ticket.

### Request

```json
{
  "ticketNumber": "TKT-1001",
  "customerName": "Demo Customer"
}
```

### Success

**Status:** `201 Created`

```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "ticketNumber": "TKT-1001",
    "customerName": "Demo Customer",
    "status": "active",
    "createdBy": "user-uuid",
    "createdAt": "2026-10-01T10:00:00Z",
    "updatedAt": "2026-10-01T10:00:00Z"
  }
}
```

### Invalid Input

**Status:** `400 Bad Request`

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid ticket data.",
    "fields": {
      "ticketNumber": "Ticket number is required.",
      "customerName": "Customer name is required."
    }
  }
}
```

The frontend must:

* prevent submission
* identify invalid fields
* display validation errors
* visually highlight invalid fields
* keep the interface usable with keyboard navigation

### Duplicate Ticket Number

**Status:** `409 Conflict`

```json
{
  "success": false,
  "error": {
    "code": "DUPLICATE_TICKET",
    "message": "A ticket with this number already exists.",
    "fields": {
      "ticketNumber": "Ticket number must be unique."
    }
  }
}
```

---

# 4. Generate QR Code

## `POST /api/tickets/:id/qr`

Generates a QR code for an existing ticket.

### Request

No request body is required.

### Success

**Status:** `201 Created`

```json
{
  "success": true,
  "data": {
    "id": "qr-uuid",
    "ticketId": "ticket-uuid",
    "qrValue": "TKT-1001",
    "status": "active",
    "createdAt": "2026-10-01T10:05:00Z",
    "updatedAt": "2026-10-01T10:05:00Z"
  }
}
```

### Ticket Not Found

**Status:** `404 Not Found`

```json
{
  "success": false,
  "error": {
    "code": "TICKET_NOT_FOUND",
    "message": "Cannot generate QR code because the ticket does not exist.",
    "fields": {}
  }
}
```

### QR Already Exists

**Status:** `409 Conflict`

```json
{
  "success": false,
  "error": {
    "code": "QR_ALREADY_EXISTS",
    "message": "A QR code already exists for this ticket.",
    "fields": {}
  }
}
```

---

# 5. Update Ticket

## `PATCH /api/tickets/:id`

Updates allowed ticket information.

### Request

```json
{
  "customerName": "Updated Customer",
  "status": "completed"
}
```

### Success

**Status:** `200 OK`

```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "ticketNumber": "TKT-1001",
    "customerName": "Updated Customer",
    "status": "completed",
    "createdBy": "user-uuid",
    "createdAt": "2026-10-01T10:00:00Z",
    "updatedAt": "2026-10-01T11:00:00Z"
  }
}
```

### Invalid Input

**Status:** `400 Bad Request`

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid ticket data.",
    "fields": {
      "status": "Invalid ticket status."
    }
  }
}
```

### Ticket Not Found

**Status:** `404 Not Found`

```json
{
  "success": false,
  "error": {
    "code": "TICKET_NOT_FOUND",
    "message": "Ticket not found.",
    "fields": {}
  }
}
```

---

# 6. Delete Ticket

## `DELETE /api/tickets/:id`

Deletes a ticket and its associated QR-code record according to the application's deletion policy.

### Success

**Status:** `204 No Content`

No response body is required.

### Ticket Not Found

**Status:** `404 Not Found`

```json
{
  "success": false,
  "error": {
    "code": "TICKET_NOT_FOUND",
    "message": "Ticket not found.",
    "fields": {}
  }
}
```

---

# Validation Rules

## Ticket Number

* Required
* Must not be empty
* Must follow the defined ticket-number format
* Must be unique
* Must not contain unsafe executable content

## Customer Name

* Required
* Must not be empty
* Must satisfy the maximum length defined by the database schema
* Must be sanitized before being stored in application state

## Ticket Status

Allowed values:

```text
active
completed
cancelled
```

Unknown status values must be rejected.

---

# Frontend Request States

Every asynchronous API operation must expose an appropriate UI state.

## Loading

```text
Loading...
```

A visible loading indicator must be displayed while waiting for the operation.

## Success

The resulting data should be displayed immediately after the operation succeeds.

## Empty

For an empty list:

```text
No data found.
```

## Error

For a failed request:

```text
Unable to load data. Please try again.
```

The interface must remain usable rather than crashing.

---

# Bad Connectivity

The application must assume that users may have slow or unreliable connectivity.

The frontend should:

* display loading indicators during asynchronous operations
* prevent accidental duplicate submissions where appropriate
* handle request failures gracefully
* preserve already available UI/data when possible
* provide a retry action after recoverable failures
* never display an uncaught application error or blank screen

---

# Security

Text inputs must be sanitized against XSS before being stored in application state.

The backend must also validate incoming data rather than trusting frontend validation.

The QR payload must contain a non-sensitive ticket reference rather than unnecessary personal information.

No API keys, credentials, or sensitive configuration values may be hardcoded.

---

# Accessibility

The eventual frontend implementation must target a 100% Lighthouse accessibility score.

Requirements include:

* semantic HTML
* keyboard-accessible interactive elements
* appropriate labels for inputs
* appropriate ARIA labels where required
* visible validation feedback
* accessible loading and error states
* sufficient text/background contrast
* logical focus order

---

# Telemetry Simulation

After a primary successful user action, the application must log:

```text
[Analytics] User interacted with Ticket QR Code Generator Worker
```

The telemetry is simulated locally and must not expose sensitive user information.

---

# API Error Codes

| Code                | Meaning                         |
| ------------------- | ------------------------------- |
| `VALIDATION_ERROR`  | Request contains invalid data   |
| `TICKET_NOT_FOUND`  | Requested ticket does not exist |
| `DUPLICATE_TICKET`  | Ticket number already exists    |
| `QR_ALREADY_EXISTS` | Ticket already has a QR code    |
| `SERVER_ERROR`      | Unexpected server-side failure  |

---

# Design Principles

* Consistent JSON response structures
* Explicit HTTP status codes
* Predictable validation errors
* User-friendly empty and error states
* Resilient behavior under unreliable connectivity
* No sensitive information in QR payloads
* API contracts remain implementation-independent
