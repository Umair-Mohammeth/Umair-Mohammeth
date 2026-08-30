# Repository instructions for Copilot

## Scope and repo reality
- This repository currently contains only `README.md` and profile/visual content.
- There is no application source tree, package manifest, or test/build/lint configuration yet.

## How to work in this repo
- Treat changes as **README/profile content edits** unless new project files are explicitly introduced.
- Keep edits minimal and preserve existing visual/HTML-heavy formatting in `README.md`.
- Do not assume a Node/Python/Java build pipeline exists; verify commands from actual repo files before running anything.

## Build, test, and lint commands
- No build, test, or lint commands are defined in the current repository state.
- If project files are added later (for example `package.json`, `pyproject.toml`, or CI workflows), derive commands from those files and add them here.

## Environment Expectations and Blockers

### When to Stop Debugging
**Distinguish code problems from environment blockers early.** If you encounter failures that repeatedly fail on the same line despite different fix attempts, check if the issue is environmental:

- **Cargo/Rust toolchain missing** (`cargo: command not found`, `cargo metadata` fails) → STOP. Ask the user to install Rust from https://rustup.rs. This is not a code problem.
- **Windows SDK/MSVC linker issues** (`kernel32.lib not found`, `link.exe` fails) → STOP. This requires Visual Studio C++ build tools installation, not code changes.
- **Tauri/Node version mismatches** (schema errors, CLI unrecognized commands) → Verify the version from package.json/tauri.conf.json, then fix the config or ask the user to upgrade. Do not loop on the same error more than twice.

### Expected Tool Versions
When the project includes a Tauri + React frontend:
- **Tauri v2** (not v1) — Use the v2 config schema in `src-tauri/tauri.conf.json`
- **Node 18+** — Required for modern build tooling
- **Rust** — Required for Tauri; installed via rustup, requires MSVC build tools on Windows

### When to Ask the User
If a blocker is environmental (missing Rust, missing MSVC tools, PATH issues, missing Node):
1. **Clearly identify** what is missing (e.g., "Rust is not installed")
2. **Do not attempt multiple workarounds** — environmental issues require user action
3. **Provide explicit recovery steps** with links (e.g., https://rustup.rs, Visual Studio Community download)
4. **Stop after the first clear diagnosis** — looping on the same error wastes time

## Known Config Patterns

### Tauri v2 Schema
When working with Tauri v2 projects, use the correct schema in `src-tauri/tauri.conf.json`:
- ✅ Correct key: `"app"` (not `"build"`)
- ✅ Correct key: `"frontendDist"` (not `"devPath"`)
- ❌ Incorrect: Tauri v1 keys like `"tauri"` as a direct property; v2 nests it under `"app"` → `"security"` → `"core"`

If you see `error on 'build': Additional properties are not allowed`, the config is using v1 schema. Inspect `package.json` for `"tauri": "^2"` to confirm v2, then update the config structure.

### Linker Errors on Windows
If you see `LINK : fatal error LNK1181: cannot open input file 'kernel32.lib'`:
- This is **not a Rust/project problem** — it's a missing Windows SDK library path
- **Solution**: Open Visual Studio Installer → Modify → check "Desktop development with C++" → Repair
- Do not attempt to patch the project — the environment must be fixed first

## Session-history friction to avoid
- Prior sessions in this workspace built a complex IDE project (Devora) and encountered repeated environment setup issues that were misdiagnosed as code problems.
- Given the repo shape, common avoidable failure is inventing project structure or automation that does not exist; always ground actions in files present in this repo.
- When debugging build/linker errors, verify the environment is set up before spending time on code changes.
