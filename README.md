# AI-Powered Cross-Repo Documentation Skills

A set of [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skills that auto-generate layered documentation from source code across multiple repositories. Built for teams where knowledge lives in code but needs to reach developers, AI agents, and business stakeholders.

📝 **Blog series**: [Part 1 — I Was Copy-Pasting Between Two AI Chats. Here's How I Fixed It.](https://medium.com/@leyb.work/i-was-copy-pasting-between-two-ai-chats-heres-how-i-fixed-it-0fa4c521b2a9>)

## The Problem

In a multi-repo microservice architecture, no single person (or AI) has full context. Developers spend weeks mapping service interactions. AI coding agents can't see past the repo they're working in. Business teams can't trace a feature through the technical stack.

These skills solve that by generating structured, layered documentation — directly from code — that both humans and AI agents can consume.

## The Pipeline

Each skill's output feeds the next, building up layers of context:

```
Source Code (per repo)
    │
    ▼ (1. dev-overview-generator)
DEV_OVERVIEW.md + DIAGRAM.mermaid
    │
    ├──▶ (2. service-integration-mapper)
    │         │
    │         ▼
    │    {service}.yaml  ← structured produces/consumes
    │         │
    │         ▼ (3. connections-builder)
    │    _connections.yaml  ← unified cross-service graph
    │         │
    │         ├──▶ (4. flow-diagram-generator) → sequence diagrams
    │         │
    │         └──▶ (6. ddd-flow-mapper) → business-language flows
    │
    └──▶ (5. topology-generator) → architecture map
```

## Skills

| # | Skill | Scope | What it does |
|---|-------|-------|-------------|
| 1 | `dev-overview-generator` | Per repo | Analyzes code → `DEV_OVERVIEW.md` + Mermaid diagram |
| 2 | `service-integration-mapper` | Per repo | Transforms DEV_OVERVIEW → structured `{service}.yaml` |
| 3 | `connections-builder` | Cross-repo | Stitches all service YAMLs → unified `_connections.yaml` |
| 4 | `flow-diagram-generator` | Cross-repo | Generates Mermaid sequence diagrams from E2E flows |
| 5 | `topology-generator` | Cross-repo | Generates high-level architecture map |
| 6 | `ddd-flow-mapper` | Cross-repo | Translates technical flows → business-language documentation |

## Two Audiences, Two Formats

**For humans:** Mermaid diagrams, business-language flows, readable overviews.

**For AI agents:** Structured YAML with explicit `produces` / `consumes` mappings that can be cross-referenced programmatically.

## Setup

Clone this repo and symlink it so Claude Code discovers the skills from any repository:

```bash
git clone <REPO_URL>
ln -s /path/to/this/repo ~/.claude/skills
```

Claude Code looks for skills in `~/.claude/skills`. The symlink makes them available globally — trigger any skill from any repo on your machine.

## Usage

### First time (full pipeline)

Run skills in order — each step's output feeds the next:

```
1. dev-overview-generator        → Run in each repo
2. service-integration-mapper    → Run on each DEV_OVERVIEW
3. connections-builder           → Run once (reads all service YAMLs)
4. flow-diagram-generator        → Run once (reads _connections.yaml)
5. topology-generator            → Run once (reads all DEV_OVERVIEWs)
6. ddd-flow-mapper               → Run once (reads _connections.yaml)
```

### Adding a new repo

1. Run `dev-overview-generator` in the new repo
2. Run `service-integration-mapper` on the new DEV_OVERVIEW
3. Re-run steps 3–6 to update cross-repo docs

### Updating an existing repo

1. Re-run `dev-overview-generator` in the changed repo
2. Re-run `service-integration-mapper` on the updated DEV_OVERVIEW
3. Re-run steps 3–6

## Output Structure

```
docs/
├── tech/
│   ├── {service}_DEV_OVERVIEW.md
│   ├── {service}_DEV_OVERVIEW_DIAGRAM.mermaid
│   ├── topology.yaml
│   ├── topology.mermaid
│   └── flows/
│       ├── _index.yaml                    ← domain index + glossary
│       ├── {domain}/{flow}.yaml           ← business-language flows
│       └── technical/
│           ├── _connections.yaml          ← unified connection graph
│           ├── {service}.yaml             ← per-service integration maps
│           └── diagrams/*.mermaid         ← sequence diagrams
```

## Blog Series

1. [Part 1 — The Problem & Approach](https://medium.com/<YOUR_MEDIUM_HANDLE>/<POST_SLUG>)
2. Part 2 — Per-Repo Skills (coming soon)
3. Part 3 — Cross-Repo Skills (coming soon)

## License

MIT

---

*Built at [myBites](https://mybites.io/) — the kind of startup that trusts its engineers to explore, experiment, and ship things like this.*