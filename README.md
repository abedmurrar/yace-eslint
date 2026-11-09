# YACE ESLint

[ESLint](https://eslint.org) in [YACE](https://github.com/abedmurrar/yace):
problems in the Problems panel and quick fixes on `Ctrl+.`, for
JavaScript, TypeScript, JSX and TSX files, using your project's own
ESLint and configuration.

It's a manifest only, with no plugin code: it tells YACE to start the
ESLint language server from VS Code's ESLint extension
(`vscode-eslint-language-server`) next to the main server for those
languages. Hover, completion and go to definition still come from the
TypeScript server.

## Install

In YACE 0.13 or later: **Extensions: Browse Plugins…** and pick ESLint, or
from a shell:

```sh
yace --install-plugin io.github.abedmurrar.yace-eslint
```

It needs the ESLint language server, once:

```sh
npm install --global vscode-langservers-extracted
```

and ESLint with a configuration in your project (`npm init @eslint/config@latest`).

## What it does

- **Starts only in ESLint projects:** for files with an `eslint.config.*`
  or `.eslintrc*` file in their folder or a folder above it, up to the
  workspace folder. Elsewhere nothing changes.
- **Uses your project's ESLint:** the server loads ESLint from the
  project's `node_modules`, with your configuration and plugins. The
  server itself comes from the project's `node_modules/.bin` if it's there,
  else from your `PATH`.
- **Problems** come with the source `eslint`, next to the TypeScript
  server's, and update as you type. The status bar shows
  `typescript + eslint`.
- **Quick fixes** (`Ctrl+.`): a rule's fix when it has one, and "Disable
  `<rule>` for this line / for the entire file".
- It doesn't format. Use Prettier, Biome or your language server for that.

Your ESLint configuration is JavaScript that ESLint runs, as it does on
the command line. Disabling or uninstalling the plugin stops the server
and removes its problems. `yace --safe-mode` doesn't start it.

## Development

`scripts/package.sh` builds `dist/yace-eslint-<version>.tar.gz` with
`yace --package-plugin`, which validates the manifest. CI packages and
installs it with the latest YACE on every push.

The end-to-end test lives in the YACE repository (`real_eslint` in
`crates/yace/tests/lsp_plugins.rs`): it installs this folder into YACE,
opens a project, and checks the problems and a quick fix:

```sh
cd path/to/yace
YACE_TEST_ESLINT=$HOME/Dev/yace-eslint:/path/to/project-with-eslint.config.js \
  cargo test -p yace --test lsp_plugins real_eslint -- --nocapture
```

To release: bump `version` in `yace-plugin.toml`, commit, and push a tag
`vX.Y.Z`. The release workflow packages it and publishes the package with
its SHA-256. Then list the new version in the YACE plugin index, from a
YACE checkout:

```sh
scripts/plugin_index.py add https://github.com/abedmurrar/yace-eslint/releases/download/vX.Y.Z/yace-eslint-X.Y.Z.tar.gz
```

## Licence

Dual-licensed under [MIT](LICENSE-MIT) or [Apache-2.0](LICENSE-APACHE), at
your option.
