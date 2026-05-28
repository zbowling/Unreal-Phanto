# Agent Instructions — Project Phanto (Unreal)

Project Phanto is an Unreal reference app for Meta Quest demonstrating Presence Platform features — scene mesh, Scene Model, Scene API objects, and Meta XR Haptics — in a "ghostly goo" mixed-reality game.

## Source-of-truth files (read these first, do not duplicate their contents in this file)

For setup, build steps, SDK versions, and project layout, read:

- `README.md` — official setup, engine/plugin requirements, run modes, device compatibility table
- `Phanto.uproject` — engine association and enabled plugins (OculusXR, MetaXRHaptics, etc.)
- `Config/DefaultEngine.ini` — NavMesh settings under `[/Script/NavigationSystem.RecastNavMesh]` and `[/Script/NavigationSystem.NavigationSystemV1]`
- `Source/Phanto/Public/PhantoBlueprintFunctionLibrary.h` — exposed C++ helpers
- `.gitattributes` — Git LFS filters; run `git lfs install` before cloning
- `LICENSE` — license terms

## Quest / Horizon-specific notes

- **Space Setup is mandatory**: the lobby preflights for a Scene Model and Spatial Data permission. Without both, the app cannot function — preserve those checks when refactoring lobby flow.
- Two supported editor paths are documented in the README (Epic Games Launcher with the MetaXR plugin, and the Meta fork of Unreal Engine). The Meta fork path uses the `oculus-5.6` branch and requires building the editor from source. Do not casually rewrite plugin or engine pins.
- Scene mesh features beyond Quest Pro / Quest 2 capability silently no-op on those devices per the device-compatibility table in the README. Verify on Quest 3 hardware before chasing what looks like missing functionality.
- NavMesh build timing is gated by `bInitialBuildingLocked=True` so the navmesh only builds after scene anchors spawn (`ReleaseInitialBuildingLock()`). Do not remove this guard.
- Haptic assets under `Plugins/MetaXRHaptics/Content/Example/` were authored in Haptics Studio and follow Meta's Haptic Design Guidelines.

## Meta Quest tooling

This repository is part of the Meta Quest / Horizon OS ecosystem (a sample, library, template, or related project — the bespoke intro above describes which). Use that intro and the source-of-truth files it references for project-specific decisions; don't restate or invent facts from memory.

When the user asks anything about Quest device behavior, build / deploy / debug / capture flows, on-device performance, or Horizon OS APIs, reach for these tools instead of generic Unreal answers:

- **`hzdb`** — Quest-aware ADB wrapper (device list, install / launch / stop, logs, screenshots, Perfetto traces, on-device docs search). Already wired up as an MCP server via `.mcp.json`, `.vscode/mcp.json`, and `.cursor/mcp.json`. Also runnable directly: `npx -y @meta-quest/hzdb <subcommand>`.
- **Meta Quest Agentic Tools** — the full skill set, including Unreal-specific skills: [github.com/meta-quest/agentic-tools](https://github.com/meta-quest/agentic-tools). Install per your client (Claude Code: `/plugin install meta-vr@meta-quest`; Gemini CLI: `gemini extensions install https://github.com/meta-quest/agentic-tools`; Cursor / VS Code: install the **Meta Horizon** extension from the Marketplace).

A few behavior expectations:

- **Read this repo's files first.** Before answering anything project-specific, read `README.md` and whichever source-of-truth files the intro above points at. Don't restate their contents in chat — quote or link instead.
- **Use `hzdb` for device-side work.** Anything that touches an attached Quest (install, launch, logs, screenshot, capture, manifest inspection) goes through `hzdb`, not raw `adb`.
- **Check live Horizon OS docs before answering API questions.** `hzdb docs search "..."` queries the live docs; training data on Horizon OS APIs goes stale fast.
- **Don't fabricate SDK / engine versions.** If a version isn't visible in this repo's files, say so rather than guessing.
