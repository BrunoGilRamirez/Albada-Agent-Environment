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

- `agent/`: role-specific agent definitions, discovered as `.opencode/agent/`
  when this repository is cloned into Albada.
- `skills/`: pinned skills discovered as `.opencode/skills/`.
- `opencode.json`: OpenCode project configuration.
- `skills-lock.json`: skill sources and integrity hashes.
- `docs/model-routing.md`: model tiers, privacy rules, and delegation policy.
- `docs/changelog/`: environment changes and migration notes.

## Install in Albada

To use this environment with a local Albada checkout on Windows, clone this
repository directly as Albada's ignored `.opencode` directory:

```powershell
$albada = 'C:\Users\bruno\Expansion\Software\albada'

git clone https://github.com/BrunoGilRamirez/Albada-Agent-Environment "$albada\.opencode"
```

Start OpenCode from the Albada directory. OpenCode loads the project
configuration from `.opencode/opencode.json`, discovers agents from
`.opencode/agent/`, and discovers skills from `.opencode/skills/`. The nested
repository is ignored by Albada's Git repository, so it remains an independent
working repository. To update it later, run:

```powershell
git -C "$albada\.opencode" pull
```

## Privacy boundary

This repository must never contain credentials, tokens, sessions, private
databases, personal records, journal or therapy content, real conversation
logs, or machine-specific secrets. Public configuration should describe the
workflow without exposing private data.

## Stability

Tags identify configurations that are known to work. Experimental changes
should be documented before they replace a stable configuration. The Albada
application does not depend on this repository to compile, test, or run.
