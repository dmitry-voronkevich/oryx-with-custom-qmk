# Repository Guidelines

## Project Structure & Module Organization

This repository combines an Oryx-exported layout with hand-written QMK changes.
Each layout has a top-level directory named for its Oryx layout ID (currently
`XlnLx/`). Keep keyboard-specific files together there:

- `keymap.c` defines layers, combos, macros, RGB behavior, and QMK hooks.
- `config.h` contains feature flags and timing/configuration constants.
- `rules.mk` enables QMK features used by the keymap.
- `keymap.json` is the Oryx layout source. Avoid manual edits unless necessary.

`Dockerfile` supplies the compiler environment. The GitHub Actions workflow in
`.github/workflows/fetch-and-build-layout.yml` fetches Oryx, merges custom
changes, and builds firmware. `qmk_firmware/` is a submodule when initialized.

## Build, Test, and Development Commands

The supported build path is **Actions → Fetch and build layout**. Supply the
Oryx layout ID and keyboard geometry; download the resulting `.bin` or `.hex`
artifact and flash it with Keymapp.

For local preparation, run:

```sh
git submodule update --init --recursive
docker build -t qmk .
```

The workflow contains the authoritative firmware-version-aware build command;
follow it when reproducing a build locally. There is no separate unit-test or
lint suite. At minimum, run `git diff --check` before committing.

## Coding Style & Naming Conventions

Match the generated QMK style in the surrounding file: two-space indentation
in function bodies, C comments for non-obvious behavior, and QMK names such as
`KC_TAB`, `LGUI(...)`, and `COMBO(...)`. Use uppercase `SNAKE_CASE` for custom
keycodes and constants (for example, `APP_SWITCH_LAYER`), and descriptive
lowercase names for combo arrays (for example, `combo4`). Keep changes focused;
do not reformat generated layout tables unnecessarily.

## Testing Guidelines

Compile the exact keyboard/layout target after changes to `keymap.c`, `config.h`,
or `rules.mk`. Manually verify mod-taps, combos, and layers on hardware; timing
and rollover behavior cannot be fully validated without the keyboard.

## Commit & Pull Request Guidelines

Use concise, imperative Conventional Commit-style subjects such as `fix: adjust
combo timing`. Existing automation uses `✨(oryx): ...` and `✨(qmk): ...` for
generated updates; do not use those prefixes for manual changes. In pull
requests, state the affected layout ID, behavior changed, build/flash result,
and any required hardware verification.
