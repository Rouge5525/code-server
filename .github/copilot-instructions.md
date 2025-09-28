```instructions
# code-server — Copilot / AI agent instructions

Keep this short and practical. Focus on the repo layout, fast ways to build/test, conventions that differ from defaults, and files the agent should read first.

1. Big picture
   - This repo packages VS Code (in `lib/vscode/`) into a remote, browser-accessible product. Primary areas:
     - `lib/vscode/` — upstream VS Code source tree (huge). Treat it as an embedded project.
     - `src/` — code-server-specific server and CLI (Node/TypeScript).
     - `ci/` — build, packaging and release scripts used by npm scripts.
     - `docs/` — user and developer guides (see `docs/README.md`).
   - Typical change types: (A) upstream VS Code changes under `lib/vscode/`, (B) server/packaging work in root `src/` and `ci/`.

2. Where to start reading
   - `package.json` (root) — top-level scripts and dev dependencies; run this to understand test/build targets.
   - `ci/` — all build and packaging flow: `ci/build/build-code-server.sh`, `build-vscode.sh`, `build-packages.sh`.
   - `lib/vscode/README.md` — architecture and developer notes for the embedded VS Code tree.
   - `docs/README.md` — quick project overview and install paths.

3. Build / test / debug (practical commands)
   - Clean: `npm run clean` (invokes `./ci/build/clean.sh`).
   - Build: `npm run build` (runs `./ci/build/build-code-server.sh`). Use `npm run build:vscode` to only rebuild the VS Code bundle.
   - Run unit tests: `npm run test:unit`; integration: `npm run test:integration`; e2e: `npm run test:e2e`.
   - Dev watch: `npm run watch` (starts a TypeScript watch for the root project). Changes in `lib/vscode/` require following the `lib/vscode/` build docs.
   - Note: many scripts are shell scripts; prefer running inside WSL / Linux-like environment or devcontainer on Windows.

4. Project conventions an agent must follow
   - Formatting & linting: repository uses Prettier and ESLint for scripts and TypeScript. See `package.json` scripts: `fmt`, `prettier`, `lint:ts`.
   - Node engine: `node 22` in `package.json` — target modern Node runtime.
   - Extensions and VS Code code lives under `lib/vscode/` and follows upstream VS Code conventions (tabs for indentation, JSDoc, nls.localize for user strings). See `lib/vscode/.github/copilot-instructions.md` for upstream style and rules.
   - Files under `lib/` are treated as vendored upstream code — prefer minimal, well-justified edits and call out when touching upstream files.

5. Integration points and external dependencies
   - The server runs an Express-based Node server (`src/`) and proxies a built VS Code web bundle from `lib/vscode/`.
   - Packaging and release are shell-script driven in `ci/` and `steps/` (RPM/DEB/docker builders). Changes to packaging must be validated with `ci/build/build-packages.sh`.
   - Tests use Jest for root `src/` and multiple test runners inside `lib/vscode/test/` — check `package.json` and `lib/vscode/test/README.md`.

6. Helpful files to inspect for any task (order matters)
   - `package.json` (root): scripts, deps.
   - `ci/build/build-code-server.sh` and `ci/build/build-vscode.sh` — canonical build flows.
   - `lib/vscode/README.md` and `lib/vscode/.github/copilot-instructions.md` — upstream guidance and style rules.
   - `docs/README.md` — project goals and quick start.

7. Quick patterns & examples
   - Adding a server API route: modify code in `src/` and update TypeScript types; run `npm run watch` and `npm run test:unit`.
   - Updating the VS Code UI/extension: change files under `lib/vscode/src/` and rebuild the VS Code bundle with `npm run build:vscode` or `./ci/build/build-vscode.sh`.
   - Packaging: run `npm run package` then inspect `release/` artifacts produced by `ci/build/build-packages.sh`.

8. Safety and scope
   - Avoid large refactors inside `lib/vscode/` unless the change is clearly required; prefer small, focused patches with test coverage.
   - When touching `ci/` scripts, document why and verify the packaging step on a Linux environment or devcontainer.

If anything here is unclear or you want the file to include more examples (test commands, sample trace logs, or specific files to avoid), tell me which sections to expand and I'll iterate.
```
