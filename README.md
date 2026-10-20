# Bootstrap Multiplatform AI Project Docs

一个用于为代码仓库建立 **AI 友好的项目规范、知识地图和多工具指令适配器** 的 Codex Skill。它把跨工具通用规范保存在项目内的共享文档中，再为 Codex、Cursor 或后续指定的 AI 编程工具生成轻量入口，减少规范重复和工具之间的冲突。

## 项目简介

本 Skill 适用于新项目初始化、已有项目补齐 AI 项目文档，以及为已初始化项目增加或维护某个 AI 工具适配器。它会先检查仓库现状，沿用已有文档和规则；只有在缺少共享规范时才建立默认的 `docs/ai/` 文档体系。

本仓库本身是 Skill 的发布与安装目录，不是被初始化项目的模板。运行 Skill 后生成的 `AGENTS.md`、`docs/ai/` 和各工具配置会写入用户指定/当前打开的目标项目。

## 功能介绍

- **共享规范源：** 通常使用 `docs/ai/AI_DEVELOPMENT_STANDARD.md` 保存跨工具通用的开发约束。
- **知识地图：** 根据项目证据建立服务关系、API 链路、数据库索引和文档同步关系；不创建没有依据的内容。
- **工具适配器：** 为 Codex、Cursor 或用户指定的其他 AI 工具生成轻量入口；先核实具体产品当前支持的指令加载方式，不把某个工具的配置格式假设成通用标准。
- **适配器注册表：** 在目标项目的 `docs/ai/ADAPTER_REGISTRY.md` 记录名称、别名、入口、文件、加载行为、限制和验证状态。后续可直接说“为项目增加 Cloud Code 适配器”等，不必重新解释共享规范架构。
- **文档同步 Hook：** 新增适配器时，如果官方资料确认该工具支持项目级 Hook，并能提醒或继续当前代理任务，默认生成项目本地 Hook 和脚本。它依据 `DOC_REGISTRY.md` 提醒更新关联文档，并检查是否遗漏；不支持时则记录限制并采用可行的规则提示方案。
- **保护现有配置：** 保留并合并已有 Hook 配置，不重写无关规则。Hook 与 MCP 是独立集成；添加文档同步 Hook 不会顺带创建或改写 MCP 配置。
- **安全处理数据库配置：** 文档中不写入真实凭证；数据库/MCP 连接属于工具适配器，仅在项目需要且目标工具支持时处理。

当前随工作流直接说明的常见入口包括 Codex 的 `AGENTS.md` 和 Cursor rules；其他工具按请求通过适配器协议扩展，不表示仓库预置了所有产品的配置。适配能力取决于目标工具的具体产品及当前版本。Skill 会按需核对官方资料并记录验证状态，不把未经验证的自动加载、Hook 或 MCP 能力当作事实。

## 安装方式

### 通过 Codex Skill Installer 安装

在 Codex 中使用内置的 `skill-installer`，指定本仓库和 Skill 路径：

```text
owner/repo: ljf941-coder/bootstrap-multiplatform-ai-docs
path: .
```

也可以在支持运行该安装脚本的本地环境执行：

```powershell
python "$HOME\.codex\skills\.system\skill-installer\scripts\install-skill-from-github.py" `
  --repo ljf941-coder/bootstrap-multiplatform-ai-docs `
  --path . `
  --name bootstrap-multiplatform-ai-docs `
  --ref master
```

安装后按 Codex 提示开始新的对话/任务，使新 Skill 可被发现。若安装工具不接受仓库根目录 `.`，可克隆仓库后将仓库根目录作为一个 Skill 文件夹手动放入 `$CODEX_HOME/skills/bootstrap-multiplatform-ai-docs/`（未设置 `CODEX_HOME` 时通常为 `~/.codex/skills/`）。

### 手动安装

将本仓库完整克隆或下载到 Codex Skills 目录，并确保 `SKILL.md` 位于以下路径：

```text
<CODEX_HOME>/skills/bootstrap-multiplatform-ai-docs/SKILL.md
```

不要只复制 `SKILL.md`：它会引用 `references/` 中的工作流和适配器协议。

## 使用方式

在 Codex 中打开目标项目，然后用自然语言提出任务；也可以显式调用：

```text
$bootstrap-multiplatform-ai-docs 为当前项目初始化 AI 开发规范和知识地图。
```

典型请求：

```text
为当前项目建立共享 AI 开发规范、知识地图和 Codex/Cursor 适配入口。
给已初始化的项目增加 Cloud Code 适配器，沿用现有共享规范和注册表。
更新数据库文档同步规则，并保留现有 Cursor rules。
为当前项目增加一个受支持的 AI 工具适配器，并按 DOC_REGISTRY 配置文档同步 Hook。
```

初始化前 Skill 会检查目标项目边界、现有指令文件和项目文档；若项目已有等价规范，会沿用而不是平行创建一份。新增工具适配时只处理该工具所需内容，并更新注册表。

## 目标项目的目录结构要求

**没有必须采用的源代码目录结构。** Skill 会根据目标项目已有约定适配；以下是推荐的默认文档布局，不是强制迁移要求：

```text
target-project/
├── AGENTS.md                         # Codex/仓库指令入口，保持简洁
├── .cursor/
│   └── rules/                        # Cursor 适配；保留已有规则和作用范围
└── docs/
    └── ai/
        ├── AI_DEVELOPMENT_STANDARD.md # 跨工具共享规范
        ├── ADAPTER_REGISTRY.md        # 已配置工具、别名及扩展记录
        ├── KNOWLEDGE_MAP.md           # 项目拓扑和文档导航
        ├── SERVICE_MAP.md             # 服务职责和依赖（适用时）
        ├── API_LINKAGE.md             # 业务/API/前后端映射（适用时）
        ├── DATABASE_INDEX.md          # 数据库索引（适用时）
        ├── DOC_REGISTRY.md            # 代码变更与文档同步关系（适用时）
        └── <service>/DOMAIN_MAP.md    # 服务领域说明（适用时）
```

只创建有仓库证据支持且对项目有用的文档。例如没有多个服务时不必创建 `SERVICE_MAP.md`；无法确认的映射应标注为待核实，不应编造。

添加其他 AI 工具时，适配文件的位置由该工具的实际加载机制决定，可能位于仓库内、项目设置中，或需要用户手动导入。Skill 会在 `ADAPTER_REGISTRY.md` 中记录入口、文件路径、自动/手动加载方式和已知限制。工具专属规则留在对应适配器中，跨工具规则仍以共享规范为准。

文档同步 Hook 是适配器级能力：只有在确认目标工具支持项目级 Hook 及其提醒/继续机制后才会生成。Hook 读取项目自己的 `DOC_REGISTRY.md`，根据实际改动提醒更新关联文档，并在支持时检查遗漏、触发有限次数的后续处理。目标工具不支持这些能力时，Skill 会记录限制并保留可用的指令级提示；不会编造 Hook 格式，也不会把某个平台的 Hook 直接套到另一个平台。

## 文件说明

| 路径 | 说明 |
| --- | --- |
| `SKILL.md` | Skill 主入口：适用场景、共享规范原则、初始化/扩展工作流及安全边界。 |
| `agents/openai.yaml` | Codex 的 Skill 展示名称、简述和默认提示语。 |
| `references/adapter-protocol.md` | 适配器识别、官方能力核实、注册表格式、实现和验证流程，以及基于 `DOC_REGISTRY.md` 的文档同步 Hook 要求。 |
| `references/bootstrap-workflow.md` | 新项目或文档大幅更新时的仓库发现方式、推荐目录、文档职责、适配器级 Hook 策略及数据库/MCP 注意事项。 |
| `README.md` | 项目介绍、安装与使用方式、目标项目布局和本仓库文件说明。 |

## 贡献与扩展

新增 AI 工具适配器时，请遵循 `references/adapter-protocol.md`：先确认具体产品身份和官方支持的加载机制；若支持项目级文档同步 Hook，则默认按 `DOC_REGISTRY.md` 生成提醒和检查能力，并在注册表记录 Hook 文件、事件、匹配器、实际保证、运行条件及验证状态。否则说明限制并使用可行的指令级同步方案。保留既有配置，不把特定工具的能力写成所有工具都支持的功能。

## License

当前仓库尚未添加许可证文件。除非仓库维护者明确选择并添加许可证，否则请不要假设代码或文档可按某个开源许可证再分发。
