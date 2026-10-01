# API Contract — Ticket QR Code Generator Worker

## Base URL

```text
/api
```

All endpoints use JSON unless otherwise specified.

## Standard Response Format

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

## 1. Get All Tickets

### `GET /api/tickets`

Returns all available tickets.

**Success — 200 OK**

```json
{
  "success": true,
  "data": [],
  "meta": {
    "total": 0
  }
}
```

If no tickets exist, the API returns an empty array rather than an error. The frontend must display:

```text
No data found.
```

**Server Error — 500**

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

## 2. Get Ticket

### `GET /api/tickets/:id`

Returns one ticket.

**Success — 200 OK**

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

**Not Found — 404**

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

## 3. Create Ticket

### `POST /api/tickets`

Creates a ticket.

### Request

```json
{
  "ticketNumber": "TKT-1001",
  "customerName": "Demo Customer"
}
```

**Success — 201 Created**

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

**Invalid Input — 400**

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

The frontend must prevent submission and highlight offending fields.

**Duplicate Ticket — 409**

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

## 4. Generate QR Code

### `POST /api/tickets/:id/qr`

Generates a QR code for an existing ticket.

No request body is required.

**Success — 201 Created**

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

**Ticket Not Found — 404**

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

**QR Already Exists — 409**

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

## 5. Update Ticket

### `PATCH /api/tickets/:id`

Updates allowed ticket fields.

### Request

```json
{
  "customerName": "Updated Customer",
  "status": "completed"
}
```

**Success — 200 OK**

Returns the updated ticket.

**Invalid Input — 400**

Returns `VALIDATION_ERROR` with field-level errors.

**Not Found — 404**

Returns `TICKET_NOT_FOUND`.

---

## 6. Delete Ticket

### `DELETE /api/tickets/:id`

Deletes a ticket and its associated QR-code record according to the application's deletion policy.

**Success — 204 No Content**

**Not Found — 404**

Returns `TICKET_NOT_FOUND`.

---

# Validation Rules

### Ticket Number

* Required
* Must not be empty
* Must follow the defined ticket-number format
* Must be unique
* Must not contain unsafe executable content

### Customer Name

* Required
* Must not be empty
* Must respect the database maximum length
* Must be sanitized against XSS before being stored in application state

### Ticket Status

Allowed values:

```text
active
completed
cancelled
```

Unknown values must be rejected.

---

# Asynchronous Operation States

The frontend must provide:

### Loading

```text
Loading...
```

A visible loading indicator must be shown during asynchronous operations.

### Empty

```text
No data found.
```

### Error

```text
Unable to load data. Please try again.
```

A retry action should be available for recoverable failures.

---

# Connectivity Requirements

The frontend must remain usable on slow or unreliable connections.

It must:

* show loading indicators
* prevent accidental duplicate submissions where appropriate
* handle failed requests gracefully
* preserve available UI/data where possible
* provide retry functionality
* avoid uncaught errors and blank screens

---

# Security Requirements

* Sanitize text input against XSS.
* Validate data on the backend as well as the frontend.
* Do not place sensitive personal information inside QR payloads.
* Do not hardcode API keys or credentials.
* Do not commit real PII.

The QR payload should contain a non-sensitive ticket reference such as:

```text
TKT-1001
```

---

# Accessibility Requirements

The future implementation must target:

**100% Lighthouse Accessibility**

Requirements:

* Semantic HTML
* Keyboard navigation
* Proper form labels
* Appropriate ARIA labels
* Accessible loading states
* Accessible error states
* Visible validation feedback
* Logical focus order
* Sufficient contrast

---

# Telemetry

After a primary action completes successfully, log:

```text
[Analytics] User interacted with Ticket QR Code Generator Worker
```

The simulated telemetry must not expose sensitive information.

---

# Error Codes

| Code                | HTTP Status | Meaning                      |
| ------------------- | ----------: | ---------------------------- |
| `VALIDATION_ERROR`  |         400 | Invalid request data         |
| `TICKET_NOT_FOUND`  |         404 | Ticket does not exist        |
| `DUPLICATE_TICKET`  |         409 | Ticket number already exists |
| `QR_ALREADY_EXISTS` |         409 | Ticket already has a QR code |
| `SERVER_ERROR`      |         500 | Unexpected server error      |
