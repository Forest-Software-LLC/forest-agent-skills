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

## No realm split

Forest has one packages tree per `forest.json`. Mounted under ReplicatedStorage, every package's source replicates to clients, including ones only the server uses. For packages that must stay on the server, use one manifest per realm, for example `Server/forest.json` and `Shared/forest.json`, and mount `Server/Packages` under ServerScriptService or ServerStorage. Run CLI commands in the folder of the manifest you mean, and commit both lockfiles.

## Package anatomy

- A package has exactly one entry point: its root module, recorded as `root` in `forest.json` (default `src/init.luau`). Whatever it returns is the whole public API.
- For several surfaces, return a table (`{ Server = require(script.Server), Client = require(script.Client) }`) or branch on `RunService:IsServer()`.
- The published archive is the folder that holds the root module, plus the top-level `LICENSE`. Dotfiles, anything matched by `.gitignore` or `.forestignore`, the dependency folder and `forest-lock.json` are left out. The limit is 10 MB.
- Inside the package, require sibling modules relatively (`require(script.Parent.Util)`). The root module reaches its dependencies as `require(script.Packages.signal)`, which works the same in the author's workspace and in every consumer's tree.
- Rojo project files are ignored. The `root` field defines the package.
- Packages are ModuleScripts. Runtime scripts (`.server.luau`, `.client.luau` and the `.lua` forms) are rejected at publish.
- Every module in the folder ships and is visible wherever the tree is mounted. "Not exported" is a convention, not a security boundary.
- Packages can include `.rbxm` model files, published with the CLI. See [Models in Packages](https://docs.forest.dev/features/models).

## Mirrored Wally packages

Packages marked Mirrored were imported from Wally, the older Roblox registry: unmodified source, open-source-licensed versions only. They install and require like any other package. Their scope is reserved for the original owner to claim.

More: [Installing & Requiring](https://docs.forest.dev/platforms/roblox/installing), [Package Anatomy](https://docs.forest.dev/platforms/roblox/anatomy), [Server & Client APIs](https://docs.forest.dev/platforms/roblox/server-client).
