# Project Adapter Registry and Extension Protocol

Use this reference when initializing a project's adapter registry or adding a tool to an existing project. The registry belongs to the project being bootstrapped (normally `docs/ai/ADAPTER_REGISTRY.md`), not to this installed skill. It preserves context across future tasks and gives users short, recognizable names to request.

## Registry format

Create a short project overview, then keep one record per configured or planned adapter. A useful record looks like this:

```markdown
## <Canonical product name>

- **Aliases:** <common names or user phrases>
- **Status:** active | planned | unavailable | needs clarification
- **Instruction entrypoint:** <file/path or project settings location>
- **Shared source:** `docs/ai/AI_DEVELOPMENT_STANDARD.md`
- **Knowledge context:** <files or discovery mechanism>
- **Adapter artifacts:** <all files created or updated>
- **Loading behavior:** <automatic, scoped, uploaded, pasted, or unknown>
- **Capabilities:** <only verified rule scoping, hooks, MCP, etc.>
- **Limitations:** <material constraints, or "None known">
- **Activation:** <what the project owner must do, if anything>
- **Verification:** <date and how behavior was verified, or "not verified">
```

Use exact product/vendor identity when known. An alias is a user-facing shortcut, not evidence that two similarly named products behave alike. Keep the registry concise; link to official product docs for details that change frequently.

## Adding an adapter

1. Read the registry and canonical standard, then inspect existing adapter files to preserve the project's naming and layout.
2. Resolve the requested name using the registry aliases and current official vendor documentation. If identity remains ambiguous, ask which product the user means before writing product-specific configuration. Do not create fictional capabilities or conflate similarly named assistants.
3. Determine the target's actual instruction surface: what repository or project files it reads, how rules are scoped, how context is attached, and whether hooks or MCP integrations are supported. Separate verified behavior from assumptions.
4. Add the thinnest practical adapter. It should point to the shared standard, explain how to load relevant knowledge docs, and carry only tool-specific syntax or setup steps. Never duplicate the entire shared standard unless the platform has no way to reference it; if a copy is required, identify it as generated and record its source.
5. Preserve other adapters and platform-specific integrations. Do not rewrite unrelated rules or run broad migrations as a side effect of adding one adapter.
6. Update the registry in the same change: canonical product name, aliases, status, entrypoint, shared source, artifacts, loading behavior, verified capabilities, limitations, activation, and verification.
7. Verify file/link consistency and any safe local parsing. Do not perform external writes, connect to databases, or run project tests unless the user requested/authorized that action.

## Initialization template

When initializing a project, include an adapter registry even if only one tool is configured. Example:

```markdown
# AI Tool Adapter Registry

Shared standard: [`AI_DEVELOPMENT_STANDARD.md`](AI_DEVELOPMENT_STANDARD.md)
Project knowledge: [`KNOWLEDGE_MAP.md`](KNOWLEDGE_MAP.md)

Adapters are tool-specific entrypoints to the shared standard. Ask for another by product name (for example, “add <tool> adapter”); existing project context should be reused.

## Codex
- **Aliases:** Codex, ChatGPT Codex
- **Status:** active
- **Instruction entrypoint:** repository-root `AGENTS.md`
- **Shared source:** `docs/ai/AI_DEVELOPMENT_STANDARD.md`
- **Knowledge context:** linked from `AGENTS.md` and `docs/ai/KNOWLEDGE_MAP.md`
- **Adapter artifacts:** `AGENTS.md`
- **Loading behavior:** repository instructions are discovered by Codex for coding tasks; confirm root/workspace context is the project
- **Capabilities:** `AGENTS.md` instructions
- **Limitations:** does not imply ordinary ChatGPT chats can read the local repository
- **Activation:** open/connect the repository in Codex
- **Verification:** <date and method>
```

The example is a starting point, not a requirement to configure Codex when the project is intended for other tools. Verify current behavior for a target product and environment before recording it as active.

## Recognizing later requests

Treat phrases such as “add <product> adapter”, “make this project work with <product>”, or a known registry alias followed by “适配” as requests to extend the existing adapter set. Reuse the project's docs, canonical standard, and established choices. Do not ask the user to restate those details. Ask only when product identity, a materially important adapter choice, or required authorization is genuinely unclear.
