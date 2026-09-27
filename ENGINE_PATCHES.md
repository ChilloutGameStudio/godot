# SpellForge engine patches

This is ChilloutGameStudio's fork of Godot for SpellForge (the game's repository: ChilloutGameStudio/spellforge,
`GodotPort/`). Branch `spellforge` sits on upstream Godot 4.8-dev6 (`8898c2b3d`); the game pins the exact fork
commit in `GodotPort/engine/VERSION` and builds it with `GodotPort/tools/build_engine.ps1`.

Rules (HANDOVER.md 3.3): every patch is small, has a reason and a measurement, and is listed here, so rebasing onto
upstream (4.8 stable, 4.9) stays manageable. Renderer changes belong here; game code stays in the GDExtension.

| # | Patch | Why | Measured gain | Commit |
|---|---|---|---|---|
| 1 | **Planet-up editor camera.** `View3DController` gains an optional frame whose up is the direction from an origin (the planet's centre) to the orbit pivot; `_to_camera_transform`, panning and the viewport's screen-to-space picking use it. `EditorInterface.set_editor_viewports_3d_planet_up(enabled, origin)`. | Godot's editor camera keeps world +Y up: on a sphere the horizon rolls up to 90°, so the world cannot be edited in the viewport (HANDOVER 5.4). | Editor only; no run-time cost. | spellforge 1 |
| 2 | **Script access to a 3D viewport's view.** `EditorInterface.get_editor_viewport_3d_state(idx)` / `set_editor_viewport_3d_state(idx, state)` (the viewport's own saved state: pivot, rotations, distance…). | The World dock flies the editor camera to a latitude and longitude; the editor-shot automation places views. | Editor only. | spellforge 1 |
| 3 | **Clip planes and grid from code.** `Node3DEditor::set_z_clip`, `set_grid_enabled`; `EditorInterface.set_editor_3d_clip(znear, zfar)`, `set_editor_3d_grid_enabled(enabled)`. | A planet needs clip planes that follow the camera's altitude (3 m to the whole globe), and a flat grid through its centre is meaningless. | Editor only. | spellforge 1 |
| 4 | **Audio released before extensions unload.** `AudioServer::release_scene_audio(timeout)`, called from `Main::cleanup` right after the main loop is deleted: stops every playback, waits (at most 250 ms) for the mixer to drop them and frees them, then takes the effects and their per-channel instances off every bus, under the mixer lock. | A GDExtension's bus effects (the game's compressors, reverbs, beds) and stream playbacks were still run by the audio thread, and freed by `AudioServer::finish()`, after their extension was deinitialised: every exit crashed (0xC0000005 in `RefCounted::unreference` from `AudioServer::finish`). | Shutdown only: clean exits (exit codes are what the checks read). | spellforge 2 |
