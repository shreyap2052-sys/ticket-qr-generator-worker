# AI Prompt Log

## Prompt 1 — Project Planning

I need to create a Ticket QR Code Generator Worker based on the given requirements. This sprint is architecture-only, so no feature code should be written yet.

Help me identify the main entities, database requirements, validation rules, error states, loading states, accessibility requirements, security requirements, and telemetry requirements.

## Prompt 2 — Database and ERD

Create a simple database schema for the Ticket QR Code Generator Worker.

The main entities should be User, Ticket, and QR Code. Define their fields, primary keys, foreign keys, unique fields, and relationships.

Also create a Mermaid ERD that I can add to my documentation.

## Prompt 3 — API Design

Create API contracts for the planned Ticket QR Code Generator Worker.

Include endpoints for creating, viewing, updating, and deleting tickets, along with QR code generation.

Include request formats, success responses, validation errors, and common HTTP status codes.

Do not write implementation code.

## Prompt 4 — Testing Plan

Create a TDD test plan for the project using Vitest.

Cover valid inputs, invalid inputs, empty states, loading states, network failures, duplicate data, XSS protection, accessibility, and telemetry.

The tests should be planned before feature implementation.

## Prompt 5 — Final Review

Review the architecture against the original requirements.

Check the database design, API contracts, validation, error handling, accessibility, security, unreliable internet handling, and testing requirements.

Keep this sprint architecture-only and identify anything that is missing.
