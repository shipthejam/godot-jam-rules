## Engine: Godot 4 (GDScript)
<!-- globs: **/*.gd, **/*.tscn, **/*.tres, project.godot, export_presets.cfg -->

**Pinned version:** Godot 4.3 or newer (kit tested on 4.7.2-stable). Check `project.godot` →
`config/features` for the exact version and never use APIs newer than it.

### Folder structure
```
project.godot
JAM.md
scenes/            # one .tscn per thing: player.tscn, enemy.tscn, main.tscn, ui/hud.tscn
scripts/           # script lives next to its scene OR here, pick one and stay consistent
autoload/          # game_state.gd (score, lives, current level), audio.gd, tuning.gd
assets/{sprites,sfx,music,fonts}/
build/web/         # export output, gitignored
```

### Conventions
- Static typing everywhere: `var speed: float = 300.0`, `func hit(dmg: int) -> void:`.
- Tuning values as `@export` on the node, or constants in the `Tuning` autoload.
- Nodes talk **up via signals, down via method calls**. Children never `get_parent().get_parent()`.
- Cross-scene state (score, lives) lives in an autoload singleton, registered in
  Project Settings → Globals → Autoload.
- Input via the Input Map (`Input.is_action_pressed("jump")`), never raw keycodes. Add actions
  in Project Settings → Input Map, and tell the user which ones to add if you can't edit
  `project.godot` safely.
- Use `%UniqueName` (scene-unique nodes) instead of long `$A/B/C` paths for UI.

### Godot 3 → 4 traps (do NOT write the left column)
| Godot 3 (wrong) | Godot 4 (right) |
|---|---|
| `KinematicBody2D` | `CharacterBody2D` |
| `move_and_slide(velocity, Vector2.UP)` | set `velocity`, then `move_and_slide()` (no args) |
| `export var x = 1` / `onready var` | `@export var x := 1` / `@onready var` |
| `yield(get_tree().create_timer(1), "timeout")` | `await get_tree().create_timer(1.0).timeout` |
| `connect("body_entered", self, "_on_hit")` | `body_entered.connect(_on_hit)` |
| `scene.instance()` | `scene.instantiate()` |
| `Tween` node + `interpolate_property` | `var t := create_tween(); t.tween_property(node, "scale", Vector2.ONE, 0.2)` |
| `setget` | `var hp: int: set(v): hp = clamp(v, 0, max_hp)` |
| `rand_range()` | `randf_range()` / `randi_range()` |

### Pitfalls
- `_physics_process` for movement/physics, `_process` for visuals. Multiply by `delta` in
  manual movement; `move_and_slide()` already accounts for it.
- `queue_free()` not `free()` on nodes that might be mid-signal.
- Collision layers vs masks: *layer* = what I am, *mask* = what I detect. Most "Area2D never
  fires" bugs are a mask mismatch or `monitoring = false`.
- Changing physics state inside a physics callback: use `set_deferred("disabled", true)` /
  `call_deferred`.
- `@onready` vars are null in `_init()`. Use `_ready()`.
- **Web export:** the web platform only runs the **Compatibility** renderer — set it at project
  creation (Project Settings → Rendering → Renderer) so what you see in the editor matches the
  browser. Keep the Web preset's **Thread Support off** (single-threaded export, available since
  4.3): no SharedArrayBuffer, no COOP/COEP headers, and the official docs call it the more
  compatible choice for itch.io. Only if you need threads: enable Thread Support plus either
  itch.io's "SharedArrayBuffer support" embed option or the preset's Progressive Web App → Enable
  (a service worker that fakes the headers).
- **C# can't export to web** in Godot 4 per the official docs (an experimental .NET web export is
  in development upstream — don't bet a jam on it). Use GDScript for web jams.
- Export templates must be installed (Editor → Manage Export Templates) and match the editor version.

### Verification steps
1. Fresh checkout: `godot --headless --path . --import` (builds the `.godot/` cache).
2. `godot --headless --path . --check-only --script res://path/to/changed.gd` for each changed
   script — prints `SCRIPT ERROR: Parse Error: ...` (e.g. `"yield" was removed in Godot 4`).
   Exit codes aren't reliable for this; grep the output for `SCRIPT ERROR`.
3. `godot --headless --path . --quit-after 120` runs the main scene ~120 frames headless and
   shows runtime errors and `print()` output; `--quit` alone only boots the project.
4. Run the main scene (F5): core verb works, win/lose triggers, restart works without reopening.
5. `godot --headless --path . --export-release "Web" build/web/index.html` succeeds
   (requires a preset named "Web" in `export_presets.cfg`, the `build/web` folder to exist, and
   export templates for the exact editor version).
6. Serve `build/web` locally (`python3 -m http.server -d build/web 8000`) and play in a browser
   once before uploading. Audio starts only after the first click — expected.
