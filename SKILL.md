---
name: bootstrap-multiplatform-ai-docs
description: >-
  Bootstrap or extend a project's shared AI development standards, knowledge
  graph, and tool-specific instruction adapters. Use for AI project docs,
  repository instructions, knowledge maps, or requests to adapt a project for
  a named AI tool. Keeps shared standards platform-neutral and existing
  tool configurations compatible.
metadata:
  short-description: Shared AI project standards and adapters
---

# Bootstrap Multiplatform AI Project Docs

Create a maintainable AI collaboration system for a repository: one platform-neutral source of project standards and knowledge, with small adapters that let each coding assistant discover and apply it using its own supported mechanisms.

Do not modify or replace another installed project-docs skill as part of this workflow. For each target repository, preserve existing rules and integrations unless the user asks to change them.

## Modes

- **Initialize** when the project needs AI-friendly instructions, a knowledge map, or domain documentation.
- **Add an adapter** when the user says to adapt the existing project for a named AI tool. Treat this as an extension of the current registry and shared standard; do not restart discovery or ask the user to restate the project context.
- **Update** when the user asks to revise existing standards, docs, or an adapter.

For adapter work, read the project's `docs/ai/ADAPTER_REGISTRY.md` (or the location linked from its root instructions) first. Follow [the adapter protocol](references/adapter-protocol.md) for recognition, research, implementation, and registry updates.

For new project documentation or a substantial knowledge-map refresh, read [the bootstrap workflow](references/bootstrap-workflow.md). Skip it when a request only adds or updates one adapter.

## Core design

1. Keep cross-tool requirements in one canonical project document, normally `docs/ai/AI_DEVELOPMENT_STANDARD.md`. It owns behavioral expectations, documentation-sync requirements, verification policy, and links to project knowledge. Avoid maintaining complete competing copies in tool-specific files.
2. Keep `AGENTS.md` as the Codex/repository instruction entrypoint: include concise mandatory rules and links to the canonical standard and relevant knowledge docs. Do not assume every other tool reads `AGENTS.md`.
3. Treat each tool configuration as an adapter. It should teach that tool where the shared standard is, how to select relevant docs, and which tool-specific features apply. Keep platform-only syntax and automation in the adapter.
4. Maintain `docs/ai/ADAPTER_REGISTRY.md` as the project-local extension record. It must let a future user request another adapter by product name or alias without re-explaining this architecture. Record exact identity, aliases, entrypoint, generated files, context/loading behavior, capabilities, limitations, and verification state. See [the adapter protocol](references/adapter-protocol.md).
5. Preserve existing `.cursor/rules`, hooks, MCP settings, and other useful configurations. Migrate or consolidate only when needed for the requested outcome; document one canonical source and make adapters refer to it where supported.

## Workflow

### 1. Inspect before asking

Scan the workspace and infer its root, services, languages, existing `AGENTS.md`, AI rule/config folders, docs, hooks, MCP configuration, API entrypoints, and DB configuration. Read repository guidance before editing. Ask only for information that cannot be inferred and materially affects the result, such as an ambiguous workspace boundary or a requested adapter whose product identity cannot be determined safely.

When the user asks to add an adapter to an existing initialized project, reuse its existing canonical standard, knowledge docs, and registry. Do not regenerate unrelated docs.

### 2. Establish the shared source

If no cross-tool standard exists, create `docs/ai/AI_DEVELOPMENT_STANDARD.md` with concise, project-specific rules. If an equivalent source already exists, preserve its name and use it as canonical rather than introducing a duplicate. Cover only rules that change project work, such as:

- how to discover service boundaries and domain docs;
- when code changes require API, schema, or other documentation updates;
- expected assumptions, scope control, and verification/reporting;
- database and external-tool constraints that apply across assistants.

Keep detailed architecture and domain facts in linked references, not in a giant rules file. Create or update root `AGENTS.md` as the Codex adapter and navigation entrypoint. Ensure its concise mandatory instructions point to the canonical standard and the relevant knowledge-map entry.

### 3. Build or extend project knowledge

Use the repository's existing doc conventions where possible. A typical `docs/ai/` set is:

- `KNOWLEDGE_MAP.md`: topology and links to deeper docs;
- `SERVICE_MAP.md`: service roles, ownership, and connections;
- `API_LINKAGE.md`: business domain to frontend/API/backend mapping;
- `DATABASE_INDEX.md`: business grouping and schema discovery pointers;
- `DOC_REGISTRY.md`: source-file patterns and docs to synchronize;
- `{service}/DOMAIN_MAP.md`: service-specific architecture and domain boundaries.

Generate only documents supported by the workspace. Prefer compact tables and Mermaid diagrams with links over copied source code or full DDL. Sample representative controllers, API modules, entities, and migrations to establish links; label uncertain mappings as drafts instead of inventing them.

### 4. Add requested tool adapters

For every requested tool, identify its exact product and current supported instruction mechanisms. Use official, current documentation for capabilities likely to change. Do not infer behavior from a similar product name. Follow the adapter protocol and create only the adapter artifacts needed for that tool. If the same name maps to multiple products, ask a concise clarification after completing independent work.

An adapter can be a tracked repository file, an out-of-repository project instruction snippet, a tool-specific rule file, or a combination. For instructions that users must paste or upload manually, generate a clearly named copy-ready artifact and tell the user where it belongs. Never claim a platform automatically loads a file unless that behavior is verified for the exact product.

### 5. Handle hooks and MCP per platform

Hooks, MCP server definitions, and other executable integrations are optional platform capabilities, not part of the shared standard. Configure them only when supported by the target tool and needed by the project. Do not assume `.cursor/mcp.json` is a universal MCP location or that a Cursor hook will run in another assistant.

For database-backed services, derive connection identity from the relevant project configuration when appropriate, keep credentials out of tracked files, provide a safe example/template, and state which database a tool is expected to use. Never index or describe a different database as a fallback. Obtain user authorization before connecting to or changing external systems when required by the available tools.

### 6. Verify and report

Check that:

- the canonical standard is identified in root instructions;
- links in the knowledge map and registry resolve;
- every adapter points to the same shared source and has its platform-specific files recorded;
- preserved existing rules are not silently contradicted by new instructions;
- examples do not contain real secrets;
- requested validations that can be run succeed, or limitations are stated plainly.

Do not run project tests or build commands unless the user asks for testing or verification. Structural checks of generated skill/project docs are allowed when relevant. Report what was created or changed, how to activate any manual adapter, and any unverified capability.

## Typical request

After initialization, users can extend the same project by saying, for example:

> 给这个项目增加 Cloud Code 适配器

Resolve the requested product against the registry and current official docs, implement only that adapter, and add its name/aliases and behavior to `ADAPTER_REGISTRY.md`. The user should not need to explain the shared-standard architecture again. If the product name is ambiguous, ask which product they mean rather than registering a guessed identity.
