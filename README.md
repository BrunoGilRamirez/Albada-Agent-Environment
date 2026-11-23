# Albada Agent Environment

This repository contains the creator's public AI-assisted development
environment for [Albada](https://github.com/BrunoGilRamirez/albada).

It records the agents, model routing, skills, permissions, and configuration
decisions used to build the application. The repository is intentionally
public so the workflow can be inspected, reviewed, and improved.

This environment is optional. Albada contributors may use OpenCode, Codex,
Claude, Copilot, local models, other tools, or no AI tools at all. Albada's
public contribution contract is defined by its own `AGENTS.md`, architecture,
tests, and review process—not by this repository.

## Contents

- `.opencode/agent/`: role-specific agent definitions.
- `.agents/skills/`: pinned skills used by the creator's environment.
- `opencode.json`: OpenCode project configuration.
- `skills-lock.json`: skill sources and integrity hashes.
- `docs/model-routing.md`: model tiers, privacy rules, and delegation policy.
- `docs/changelog/`: environment changes and migration notes.

## Privacy boundary

This repository must never contain credentials, tokens, sessions, private
databases, personal records, journal or therapy content, real conversation
logs, or machine-specific secrets. Public configuration should describe the
workflow without exposing private data.

## Stability

Tags identify configurations that are known to work. Experimental changes
should be documented before they replace a stable configuration. The Albada
application does not depend on this repository to compile, test, or run.
