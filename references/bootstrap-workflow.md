# Project Documentation Bootstrap Workflow

Use this reference for first-time setup or when the user requests a substantial refresh of a project's AI documentation. Keep existing naming/layout when practical; the paths below are defaults, not a migration mandate.

## Read-only discovery

1. Determine workspace root and direct subprojects (for example, directories with `pom.xml`, `package.json`, `go.mod`, or solution/project files).
2. Find existing `AGENTS.md`, AI instructions, tool-specific rule folders, hooks, MCP configs, knowledge maps, API docs, SQL/migration docs, and ignore files.
3. Identify frontend API roots and proxies and connect them to backend context paths/controllers. Sample representative APIs/controllers; mark incomplete mappings as drafts.
4. For Java/Spring services, inspect applicable `application*.yml`/`yaml` files for datasource URL and credentials only as needed to document the DB binding or prepare a local MCP adapter. Respect active/local profile conventions and dynamic datasource master configuration.
5. Identify documentation dependencies, such as controllers to API linkage, API modules to frontend maps, and entities/mappers/migrations to database indexes.

Prefer `rg`/`rg --files` or equivalent targeted file discovery. Do not call a global DB MCP before establishing which project DB is authoritative.

## Typical docs layout

```text
project/
├── AGENTS.md                         # Codex/repository entrypoint
├── docs/ai/
│   ├── AI_DEVELOPMENT_STANDARD.md    # canonical cross-tool rules
│   ├── ADAPTER_REGISTRY.md           # extension record and known aliases
│   ├── KNOWLEDGE_MAP.md              # topology and links
│   ├── SERVICE_MAP.md                # service responsibilities
│   ├── API_LINKAGE.md                # business/frontend/API/backend links
│   ├── DATABASE_INDEX.md             # business grouping and schema source
│   ├── DOC_REGISTRY.md               # code patterns → docs to synchronize
│   └── <service>/DOMAIN_MAP.md
└── <tool-specific files>             # e.g. .cursor/rules/, optional adapters
```

Do not create empty or speculative maps. Include only docs supported by repository evidence, and add missing pieces incrementally when they become useful.

## Document responsibilities

- `AGENTS.md` is a concise repository instruction hub. State mandatory behavior there and link to the canonical standard and knowledge map. Avoid duplicating the full domain docs.
- `AI_DEVELOPMENT_STANDARD.md` contains cross-tool rules: project scope, relevant-doc discovery, behavior expectations, documentation-sync triggers, verification guidance, and data/tool boundaries.
- `KNOWLEDGE_MAP.md` uses a Mermaid topology if it clarifies service relationships and links to detailed docs.
- `SERVICE_MAP.md` records each service's role and key dependencies.
- `API_LINKAGE.md` maps business domain, frontend views/pages, API modules, backend routes/controllers, and notes. Example columns: `业务域 | Admin views | Admin API | H5 pages | H5 API | Backend Controller | 备注`.
- `DATABASE_INDEX.md` groups tables by business domain, identifies the configured database, and points to a suitable live schema source. Do not embed full DDL; do not invent columns or build an index from a different DB.
- `DOC_REGISTRY.md` maps code patterns to docs to update. Adapt patterns to actual repository structure. Typical relationships include `*Controller.java` → API linkage/domain map, frontend `src/api/**` → API linkage, entities/Mapper XML/SQL migrations → database index or migration docs.
- `{service}/DOMAIN_MAP.md` holds deeper service architecture/domain facts; link rather than copying its content into the global map.
- `ADAPTER_REGISTRY.md` is project-local durable context. Follow the [adapter protocol](adapter-protocol.md).

## Rule and automation migration

Keep existing tool rules that still apply. Extract only genuinely shared requirements into the canonical standard, then make tool-specific entrypoints point to it. Avoid conflicting duplicate baselines. Preserve scoped domain rules and globs in the tool adapter that understands them.

Documentation synchronization can be enforced by instructions, hooks, or both. When an adapter is requested, follow the adapter protocol: generate project-scoped doc-sync hooks by default if the exact tool supports them; otherwise use rules-only and record that synchronization is not automatically triggered. During initialization, apply this behavior to every requested adapter. A hook should remind about relevant docs based on changed paths and check for missing doc updates; it must not trigger unrelated refactors or claim semantic correctness from a file-change check alone. Do not assume one tool's hook format or lifecycle works in another tool.
## Database/MCP notes

MCP is a tool-specific adapter, not a shared standard. For each backend-owned database, record the expected host/profile/database without exposing passwords. If the target MCP server needs project configuration:

- put real credentials only in a local ignored file or approved secret store;
- include a tracked example with placeholders when useful;
- follow the target client's actual config discovery rules;
- keep distinct backends bound to their own datasource;
- after edits, state whether a reload/restart is needed and verify only after the target client has loaded the intended server.

Do not include credentials in `AGENTS.md`, the shared standard, registry, or committed examples. If DB access fails or appears to point at the wrong schema, stop and correct the binding instead of silently using another DB.
