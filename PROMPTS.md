# AI Prompt Log

These prompts were used during the architecture and planning of the Ticket QR Code Generator Worker.

## Prompt 1 — Requirements Analysis

I have reviewed the client requirements for the Ticket QR Code Generator Worker. Before starting implementation, I want to make sure the requirements are converted into clear technical requirements.

Help me identify the main data entities, relationships, validation requirements, unhappy paths, accessibility requirements, security considerations, and telemetry requirements that should be reflected in the architecture.

Do not suggest feature implementation yet.

## Prompt 2 — Database Design

I have identified User, Ticket, and QR Code as the main entities for the system.

Help me review this structure and determine the appropriate fields, primary keys, foreign keys, unique constraints, and relationships. I also want the QR code to reference the ticket without exposing unnecessary customer information.

Once the structure is finalized, help me represent it as a Mermaid ERD.

## Prompt 3 — API Contract Review

The database structure is now defined. I want to plan the API before implementation.

Help me define the required endpoints for ticket creation, retrieval, updating, deletion, and QR code generation. For each endpoint, define the expected request, success response, validation errors, and relevant HTTP status codes.

Keep this as an API contract only; no backend implementation.

## Prompt 4 — Unhappy Paths and TDD

The assignment requires the application to handle invalid input, empty results, loading states, and unreliable internet connections.

Help me turn these requirements into a test plan using Vitest. Include tests for validation, duplicate records, failed network requests, XSS sanitization, accessibility, and the required analytics message.

The tests should be planned before implementation so the project can follow a TDD workflow.

## Prompt 5 — Architecture Review

I have completed the initial database schema, ERD, API contracts, and test plan.

Review these planned components against the original assignment requirements and point out any gaps or inconsistencies. Focus on requirements that could cause problems during the future implementation, especially validation, security, accessibility, error handling, and unreliable connectivity.

This sprint is still architecture-only, so recommend documentation changes rather than feature code.
