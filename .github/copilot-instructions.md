# Copilot Instructions for niri-taskbar

## Project Overview
- **niri-taskbar** is a Rust dynamic library (cdylib) providing a custom taskbar module for [Waybar](https://github.com/Alexays/Waybar), designed to work with the [Niri](https://github.com/YaLTeR/niri) Wayland compositor.
- The main feature is deterministic window ordering (by workspace index, then window ID/creation time), improving over Waybar's built-in taskbar.
- Integrates with Niri via IPC (see `src/niri/`), and with Waybar via CFFI (see `waybar_cffi` usage in `src/lib.rs`).
- Supports advanced features: per-app highlighting, notification integration, multiple display modes, and custom CSS styling.

## Key Components
- **src/lib.rs**: Entrypoint, module registration, GTK UI setup, async event loop, and main data flow.
- **src/state.rs**: Global state, configuration, icon cache, and Niri IPC client.
- **src/config.rs**: Deserializes and manages taskbar configuration (display modes, app rules, notification settings).
- **src/niri/**: Handles Niri IPC, window state, and workspace logic.
- **src/notify.rs**: Listens for D-Bus notifications, enriches and streams them for UI highlighting.
- **src/button.rs, src/icon.rs**: UI elements and icon handling.
- **src/style.css**: Default CSS for taskbar appearance.

## Developer Workflows
- **Build**: `cargo build --release` (produces `target/release/libniri_taskbar.so`)
- **Test**: No automated tests; manual testing via Waybar integration is standard.
- **Debug**: Uses `tracing`/`tracing-subscriber` for logging; set `RUST_LOG` or `RUST_LOG_STYLE` for debug output.
- **Install**: Copy `.so` to a location Waybar can load, then configure in Waybar's config as a CFFI module.

## Configuration & Usage
- Configure in Waybar's config under `cffi/niri-taskbar` (see README for JSONC examples).
- Supports `apps` rules for per-app highlighting (regex on window titles, custom CSS classes).
- `display_mode` controls window grouping (everything, by_output, by_workspace, workspace_buttons).
- `notifications` enables D-Bus notification listening and urgent highlighting.
- Custom CSS classes can be styled in `style.css` or user config.

## Patterns & Conventions
- All async/event-driven logic is handled via `futures` and `glib::MainContext`.
- State is passed via the `State` struct (Arc/Mutex for shared state).
- IPC with Niri is abstracted in `src/niri/` and uses the `niri-ipc` crate.
- Notification integration is via D-Bus (see `src/notify.rs`).
- Prefer explicit window/app IDs and workspace indices for ordering and grouping.
- All UI is GTK-based; use CSS classes for styling and state indication.

## Integration Points
- **Waybar**: Loaded as a CFFI module, configured in Waybar's JSON config.
- **Niri**: Communicates via IPC for window/workspace state.
- **D-Bus**: Listens for notifications to trigger UI highlights.

## References
- See [README.md](../README.md) for user-facing configuration and usage details.
- See `src/lib.rs` and `src/config.rs` for entrypoint and config structure.
- See `src/niri/` for IPC and window state logic.

---

If you add new features or change integration points, update this file and the README accordingly.
