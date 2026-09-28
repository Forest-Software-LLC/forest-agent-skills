# Forest for Claude

[Forest](https://forest.dev) is the package manager for Roblox and UEFN. This plugin gives Claude two things:

- **The Forest MCP server** (`https://api.forest.dev/mcp`), so Claude can search packages, read their READMEs and source, check licenses, list your own packages, and publish new ones.
- **A skill that says when to use it.** Before writing general-purpose code such as a signal, promise, networking or data store module, Claude checks Forest for a well-used package and shows you what it found. When you've written a self-contained module other projects could reuse, Claude suggests publishing it, and publishes only when you say so. The skill also covers installing, requiring and publishing on Roblox (Luau) and UEFN (Verse).

## Install

### Claude Code

```
/plugin marketplace add Forest-Software-LLC/forest-claude-plugin
/plugin install forest@forest
```

Or from your shell:

```bash
claude plugin marketplace add Forest-Software-LLC/forest-claude-plugin
claude plugin install forest@forest
```

### Claude on the web, desktop and Cowork

Open **Customize > Plugins**, choose **Add > Add marketplace**, and enter `Forest-Software-LLC/forest-claude-plugin`. Add the **forest** plugin, then open its **Connectors** tab to add and connect the Forest connector.

### Other assistants

Any client that supports remote MCP servers can connect to `https://api.forest.dev/mcp` directly. The setup for Cursor, VS Code and others is in the [Forest docs](https://docs.forest.dev/features/ai-agents).

## Signing in

Public packages work without an account. The first time Claude needs your account, for your private packages or to publish, your client opens a Forest sign-in page that shows the app, your account and the permissions it asks for. Publishing is a separate permission, requested the first time Claude tries to publish, and your client asks you to confirm every publish.

Apps you've connected are listed under **Connected apps** in your Forest profile settings, where you can disconnect them at any time.

## Data

The plugin itself contains no code; it points Claude at Forest's MCP server and adds instructions. When Claude uses the Forest tools, it sends Forest what the tool needs: search text, package names, and, for a publish you approve, the package's files and details. Signed in, requests carry an access token for your account. Forest's [privacy policy](https://forest.dev/legal/privacy) covers how that data is handled.

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
