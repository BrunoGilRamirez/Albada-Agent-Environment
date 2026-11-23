# Configuration Guide

The repository is the historical source for the creator's OpenCode setup.
When used locally with Albada, clone it as the `.opencode/` directory of the
Albada checkout. It bundles the agents, skills, `opencode.json`, and
`skills-lock.json`; those local files are ignored by Albada's Git
configuration.

The application remains independent of this environment. A contributor can
work without installing any of these files.

## Safe changes

Keep changes small, explain permission changes, and create a rollback tag
before changing a stable model or delegation path.
