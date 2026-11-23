# Albada Agent Environment Instructions

This repository contains the public, versioned AI-assisted development
environment used by the creator of Albada.

## Rules

- Keep all configuration and documentation in English.
- Never add credentials, tokens, sessions, private databases, personal
  records, journal or therapy content, real conversation logs, or secrets.
- Keep project-specific product rules in Albada's public `AGENTS.md`; this
  repository may reference them but must not replace them.
- Treat model selection, routing, permissions, and skills as auditable
  configuration. Explain material changes in the changelog.
- Prefer the smallest configuration change that solves the problem.
- Preserve working configurations with Git tags before experimental changes.
- Do not make Albada depend on OpenCode or any specific model to build or test.

## Review checklist

- Is the change free of private content and secrets?
- Does it preserve the public Albada contribution boundary?
- Are model, permission, and retention implications documented?
- Is the rollback path clear?
- Has the relevant configuration been checked for syntax and consistency?
