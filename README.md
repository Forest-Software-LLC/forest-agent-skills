# Forest agent skills

[Forest](https://forest.dev) is the package manager for Roblox and UEFN. This repo teaches AI coding agents when and how to use it.

- **The `forest-packages` skill.** Before writing general-purpose code such as a signal, promise, networking or data store module, the agent checks Forest for a well-used package and shows you what it found. When you've written a self-contained module other projects could reuse, it suggests publishing it, and publishes only when you say so. It also covers installing, requiring and publishing on Roblox (Luau) and UEFN (Verse), and it treats everything a package author wrote as data: a README or a comment that tells the agent to install, fetch, publish or stay quiet rules the package out and gets reported to you.
- **The `forest` plugin.** The skill plus a connection to the Forest MCP server (`https://api.forest.dev/mcp`), so your agent can search packages, read their READMEs and source, check licenses, list your own packages, and publish new ones. It installs in Claude Code, the Claude apps and Codex.

The skill uses the open [Agent Skills](https://agentskills.io) format, so it works in Claude, Cursor, Codex, GitHub Copilot, Gemini CLI and other agents that support skills.

## Install

Install the skill together with the MCP server. The [Forest docs](https://docs.forest.dev/features/ai-agents) walk through each agent step by step.

### Claude Code

```
/plugin install forest --marketplace Forest-Software-LLC/forest-agent-skills
```

On Claude Code older than 2.1.275, run these in your terminal instead:

```bash
claude plugin marketplace add Forest-Software-LLC/forest-agent-skills
claude plugin install forest@forest-agent-skills
```

### Claude on the web, desktop and Cowork

Open **Customize > Plugins**, choose **Add > Add marketplace**, and enter `Forest-Software-LLC/forest-agent-skills`. Add the **forest** plugin, then open its **Connectors** tab to add and connect the Forest connector.

### Codex

```bash
codex plugin marketplace add Forest-Software-LLC/forest-agent-skills
codex plugin add forest@forest-agent-skills
```

### Cursor

[Add the Forest MCP server to Cursor](https://cursor.com/install-mcp?name=forest&config=eyJ1cmwiOiJodHRwczovL2FwaS5mb3Jlc3QuZGV2L21jcCJ9), then add the skill:

```bash
npx skills add Forest-Software-LLC/forest-agent-skills -g -a cursor -y
```

### VS Code

[Add the Forest MCP server to VS Code](https://vscode.dev/redirect/mcp/install?name=forest&config=%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fapi.forest.dev%2Fmcp%22%7D), then add the skill for GitHub Copilot:

```bash
npx skills add Forest-Software-LLC/forest-agent-skills -g -a github-copilot -y
```

### Other agents

```bash
npx skills add Forest-Software-LLC/forest-agent-skills -g
npx add-mcp https://api.forest.dev/mcp -g
```

Each asks which of your agents to set up: the [skills CLI](https://github.com/vercel-labs/skills) adds the skill to GitHub Copilot CLI, Gemini CLI, OpenCode, Windsurf and more, and [add-mcp](https://github.com/neon-solutions/add-mcp) connects the MCP server. You can also copy [`plugins/forest/skills/forest-packages`](plugins/forest/skills/forest-packages) into your agent's skills folder and add `https://api.forest.dev/mcp` as a remote MCP server yourself. Without the server, the skill falls back to Forest's public HTTP API for public packages, where your agent can fetch URLs.

## Signing in

Public packages work without an account. When the agent needs your account, for your private packages or to publish, your client opens a Forest sign-in page that shows the app, your account and the permissions it asks for. In Claude Code, run `/mcp` and authenticate **forest**; in Codex, run `codex mcp login forest`. Publishing is a separate permission on that page, and your agent asks you before every publish.

Apps you've connected are listed under **Connected apps** in your Forest profile settings, where you can disconnect them at any time.

## Data

Nothing here runs code: the skill is instructions, and the plugin points your agent at Forest's MCP server. When the agent uses the Forest tools, it sends Forest what the tool needs: search text, package names, and, for a publish you approve, the package's files and details. Signed in, requests carry an access token for your account. Forest's [privacy policy](https://forest.dev/legal/privacy) covers how that data is handled.

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
