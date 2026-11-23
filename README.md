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

## Install in Albada

To use this environment with a local Albada checkout on Windows, clone this
repository and copy its project configuration into Albada:

```powershell
$environment = 'C:\Users\bruno\Expansion\Software\Albada Agent Environment'
$albada = 'C:\Users\bruno\Expansion\Software\albada'

git clone https://github.com/BrunoGilRamirez/Albada-Agent-Environment $environment
New-Item -ItemType Directory -Force "$albada\.opencode", "$albada\.agents" | Out-Null
Copy-Item -Recurse -Force "$environment\.opencode\agent" "$albada\.opencode\agent"
Copy-Item -Force "$environment\.opencode\.gitignore", "$environment\.opencode\package.json", "$environment\.opencode\package-lock.json" "$albada\.opencode"
Copy-Item -Recurse -Force "$environment\.agents\skills" "$albada\.agents\skills"
Copy-Item -Force "$environment\opencode.json", "$environment\skills-lock.json" $albada
```

Start OpenCode from the Albada directory. The copied configuration is ignored
by Albada's Git repository, so it does not become part of the application
source. To update the local environment later, pull this repository and repeat
the copy step.

## Privacy boundary

This repository must never contain credentials, tokens, sessions, private
databases, personal records, journal or therapy content, real conversation
logs, or machine-specific secrets. Public configuration should describe the
workflow without exposing private data.

## Stability

Tags identify configurations that are known to work. Experimental changes
should be documented before they replace a stable configuration. The Albada
application does not depend on this repository to compile, test, or run.
