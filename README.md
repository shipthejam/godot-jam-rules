# Godot 4 Jam Rules for AI Agents — free sample by ShipTheJam

Drop-in agent rules that stop Cursor, Claude Code, Codex and Copilot from writing **Godot 3
code in your Godot 4 project** — and keep them focused on shipping a jam game in 48 hours.

- `KinematicBody2D`, `yield`, `export var`, `instance()`… a trap table the agent must respect
- Static-typed GDScript conventions, signals-up/calls-down, autoload state
- Web export that actually works on itch.io (Compatibility renderer, threads, SharedArrayBuffer)
- Headless verification commands the agent runs/asks you to run after changes
- Scope rules: playable build by hour 6, one core verb, small diffs, a `JAM.md` project memory
- **One source of truth:** edit `AGENTS.md`, regenerate every tool's file with one command

## Install (Python 3, no dependencies)
```bash
git clone https://github.com/shipthejam/godot-jam-rules
python3 godot-jam-rules/sync-rules.py init --engine godot --dest path/to/your-godot-project
```
Your project gets:
```
AGENTS.md                         # edit this one
JAM.md                            # theme, deadline, core verb, cut list
.cursor/rules/gjak-core.mdc       # Cursor, always on
.cursor/rules/gjak-engine-godot-4-gdscript.mdc   # Cursor, auto-attached to .gd/.tscn
CLAUDE.md                         # Claude Code
.github/copilot-instructions.md   # Copilot
.github/instructions/gjak-engine-godot-4-gdscript.instructions.md
```
After editing `AGENTS.md`: `python3 godot-jam-rules/sync-rules.py path/to/your-godot-project`.

### Cursor
Open the project. The core rule is always applied; the Godot rule attaches when `.gd`, `.tscn`,
`.tres` or `project.godot` is in context. (Uses the current `.cursor/rules/*.mdc` format, not
the deprecated `.cursorrules`.)

### Claude Code
Run `claude` in the project. `CLAUDE.md` loads automatically. Optional: give it eyes with the
Godot MCP server — `claude mcp add godot -- npx @coding-solo/godot-mcp`
([Coding-Solo/godot-mcp](https://github.com/Coding-Solo/godot-mcp)).

### Codex
Codex reads `AGENTS.md` directly. Nothing else to do.

### GitHub Copilot
VS Code picks up `.github/copilot-instructions.md` and the path-scoped `.instructions.md`.

## First prompt to try
```
Read AGENTS.md and JAM.md. Jam theme is "<theme>", deadline <YYYY-MM-DD HH:MM TZ>.
Give me 5 one-line pitches scored for 48h build risk, then fill in JAM.md for the best one.
```

## Want the whole jam team?
**[ShipTheJam — Game Jam Agent Kit](https://shipthejam.com/#buy)** (€24 launch price, regular €29) adds Phaser 3 + TypeScript and Roblox/Luau (Rojo)
packs, 6 agent roles (designer, gameplay coder, debugger, juice/polish, build & deploy with
butler, itch.io page writer), an hour-by-hour 48h playbook, a launch checklist, MCP configs and
a Phaser starter. This repo stays free and maintained either way.

## Contributing
Found the agent writing a Godot 3 API we don't catch? Open an issue with the bad line and the
Godot 4 fix — the trap table grows from real mistakes.

## License
MIT — see `LICENSE`.
