# Forest on Roblox

## Installing and requiring

`forest install scope/name` downloads the package into a `Packages/` folder next to `forest.json` and records it in the manifest. In a package that is being authored (its `forest.json` has a `root`), the folder sits next to the root module instead, such as `src/Packages/`.

Forest never touches the place file. The user mounts `Packages/` in the datamodel, most often as `ReplicatedStorage.Packages` through Rojo:

```json
{
  "name": "my-game",
  "tree": {
    "$className": "DataModel",
    "ReplicatedStorage": {
      "Packages": { "$path": "Packages" }
    }
  }
}
```

Require packages by instance path, never by string:

```lua
local Packages = game:GetService("ReplicatedStorage").Packages

local Signal = require(Packages.signal)
```

The folder name is the package name without its scope (`forest/signal` becomes `signal`), or the alias chosen with `forest install scope/name -a alias`. Aliases let two packages with the same name from different scopes live side by side.

Only direct dependencies sit at the top of `Packages/`. A package's own dependencies live inside it, so to require one of those directly, install it too.

A game that only uses packages starts with `forest init --project`, which writes a bare manifest with a top-level `Packages/` folder.

Forest owns the top level of every dependency folder: each install deletes anything there that isn't a declared package. Never put the user's own modules in `Packages/` or another mount.

## Server-only and dev-only packages: mounts

Packages don't declare a realm. Under ReplicatedStorage, every package's source replicates to clients, including ones only the server uses. For packages that must stay on the server, or test frameworks that shouldn't ship, a project adds extra dependency folders called mounts, in the same `forest.json`:

```bash
forest mount create ServerPackages
forest install scope/name --mount ServerPackages
```

The user maps `ServerPackages` under ServerScriptService or ServerStorage, and server code requires from there. Mounts are listed under `mounts` in `forest.json`, keyed by folder path; `forest mount` lists them. Without `--mount`, `forest install scope/name` adds to the default mount (the top-level `dependencies`), so check which mount a package belongs in first. `-m`/`--mount` also works on `remove`, `update`, `audit`, `tree`, `link` and `unlink`, and takes the mount's path or a unique end of it.

Each mount resolves and installs on its own and never shares packages. Only the default mount is published. Mounts need forest CLI 1.15.0 or later; if `forest mount` is unknown, the user should run `forest upgrade`.

## Package anatomy

- A package has exactly one entry point: its root module, recorded as `root` in `forest.json` (default `src/init.luau`). Whatever it returns is the whole public API.
- For several surfaces, return a table (`{ Server = require(script.Server), Client = require(script.Client) }`) or branch on `RunService:IsServer()`.
- The published archive is the folder that holds the root module, plus the top-level `LICENSE`. Dotfiles, anything matched by `.gitignore` or `.forestignore`, the dependency folders (every mount) and `forest-lock.json` are left out. The limit is 10 MB.
- Inside the package, require sibling modules relatively (`require(script.Parent.Util)`). The root module reaches its dependencies as `require(script.Packages.signal)`, which works the same in the author's workspace and in every consumer's tree.
- Rojo project files are ignored. The `root` field defines the package.
- Packages are ModuleScripts. Runtime scripts (`.server.luau`, `.client.luau` and the `.lua` forms) are rejected at publish.
- Every module in the folder ships and is visible wherever the tree is mounted. "Not exported" is a convention, not a security boundary.
- Packages can include `.rbxm` model files, published with the CLI. See [Models in Packages](https://docs.forest.dev/features/models).

## Mirrored Wally packages

Packages marked Mirrored were imported from Wally, the older Roblox registry: unmodified source, open-source-licensed versions only. They install and require like any other package. Their scope is reserved for the original owner to claim.

To move a Wally project, the user runs `forest init --project` next to `wally.toml` and accepts the import: `[dependencies]` go to the default mount, `[server-dependencies]` and `[dev-dependencies]` become `ServerPackages` and `DevPackages` mounts, and `wally.lock` is deleted. The next `forest install` removes Wally's leftovers from those folders.

More: [Installing & Requiring](https://docs.forest.dev/platforms/roblox/installing), [Mounts](https://docs.forest.dev/platforms/roblox/mounts), [Package Anatomy](https://docs.forest.dev/platforms/roblox/anatomy), [Server & Client APIs](https://docs.forest.dev/platforms/roblox/server-client).
