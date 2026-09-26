# SpellForge engine patches

This is ChilloutGameStudio's fork of Godot for SpellForge (the game's repository: ChilloutGameStudio/spellforge,
`GodotPort/`). Branch `spellforge` sits on upstream Godot 4.8-dev6 (`8898c2b3d`); the game pins the exact fork
commit in `GodotPort/engine/VERSION` and builds it with `GodotPort/tools/build_engine.ps1`.

Rules (HANDOVER.md 3.3): every patch is small, has a reason and a measurement, and is listed here, so rebasing onto
upstream (4.8 stable, 4.9) stays manageable. Renderer changes belong here; game code stays in the GDExtension.

| # | Patch | Why | Measured gain | Commit |
|---|---|---|---|---|
