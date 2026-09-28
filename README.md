# Forest agent skills

[Forest](https://forest.dev) is the package manager for Roblox and UEFN. This repo teaches AI coding agents when and how to use it.

- **The `forest-packages` skill.** Before writing general-purpose code such as a signal, promise, networking or data store module, the agent checks Forest for a well-used package and shows you what it found. When you've written a self-contained module other projects could reuse, it suggests publishing it, and publishes only when you say so. It also covers installing, requiring and publishing on Roblox (Luau) and UEFN (Verse).
- **The `forest` plugin for Claude.** The skill plus a connection to the Forest MCP server (`https://api.forest.dev/mcp`), so Claude can search packages, read their READMEs and source, check licenses, list your own packages, and publish new ones.

The skill uses the open [Agent Skills](https://agentskills.io) format, so it works in Claude, Cursor, Codex, GitHub Copilot, Gemini CLI and other agents that support skills.

## Install

### Claude Code

```
/plugin marketplace add Forest-Software-LLC/forest-agent-skills
/plugin install forest@forest-agent-skills
```

Claude Code 2.1.275 and later can do both in one step:

```
/plugin install forest --marketplace Forest-Software-LLC/forest-agent-skills
```

Or from your shell:

```bash
claude plugin marketplace add Forest-Software-LLC/forest-agent-skills
claude plugin install forest@forest-agent-skills
```

### Claude on the web, desktop and Cowork

Open **Customize > Plugins**, choose **Add > Add marketplace**, and enter `Forest-Software-LLC/forest-agent-skills`. Add the **forest** plugin, then open its **Connectors** tab to add and connect the Forest connector.

### Other agents

```bash
npx skills add Forest-Software-LLC/forest-agent-skills
```

The [skills CLI](https://github.com/vercel-labs/skills) installs the skill into Cursor, Codex, GitHub Copilot, Gemini CLI, OpenCode, Windsurf and more; it asks which agents, or pass `--agent`. You can also copy [`plugins/forest/skills/forest-packages`](plugins/forest/skills/forest-packages) into your agent's skills folder.

Then connect the Forest MCP server, `https://api.forest.dev/mcp`, as a remote MCP server. The [Forest docs](https://docs.forest.dev/features/ai-agents) show the setup for Cursor, VS Code and other clients. Without it, the skill falls back to Forest's public HTTP API for public packages, where your agent can fetch URLs.

## Signing in

Public packages work without an account. The first time the agent needs your account, for your private packages or to publish, your client opens a Forest sign-in page that shows the app, your account and the permissions it asks for. Publishing is a separate permission, requested the first time the agent tries to publish, and your client asks you to confirm every publish.

Apps you've connected are listed under **Connected apps** in your Forest profile settings, where you can disconnect them at any time.

## Data

Nothing here runs code: the skill is instructions, and the plugin points Claude at Forest's MCP server. When the agent uses the Forest tools, it sends Forest what the tool needs: search text, package names, and, for a publish you approve, the package's files and details. Signed in, requests carry an access token for your account. Forest's [privacy policy](https://forest.dev/legal/privacy) covers how that data is handled.

## Contributing

Issues and pull requests are welcome. Test a change locally with:

```bash
claude plugin validate .
claude --plugin-dir ./plugins/forest
```

The eval suite in `plugins/forest/evals` checks that the skill searches Forest before writing a general-purpose module, skips the search for game-specific code, and suggests publishing a reusable module. It answers the Forest tools from mocks, so it needs no Forest account:

```bash
claude plugin eval plugins/forest
```

## License

[MIT](LICENSE)
