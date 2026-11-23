# Configuration Guide

The repository is the historical source for the creator's OpenCode setup.
When used locally with Albada, copy or synchronize the relevant `.opencode/`,
`.agents/`, `opencode.json`, and `skills-lock.json` files into the Albada
checkout. Those local copies are ignored by Albada's Git configuration.

The application remains independent of this environment. A contributor can
work without installing any of these files.

## Safe changes

Keep changes small, explain permission changes, and create a rollback tag
before changing a stable model or delegation path.
