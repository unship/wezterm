# WezTerm Development

## Cursor Cloud specific instructions

### Project overview
WezTerm is a GPU-accelerated terminal emulator and multiplexer written in Rust. It is a Cargo workspace with ~69 internal crates producing 4 binaries: `wezterm`, `wezterm-gui`, `wezterm-mux-server`, and `strip-ansi-escapes`.

### Build and run commands
See `Makefile` for standard targets: `make build`, `make test`, `make check`, `make fmt`. The `CONTRIBUTING.md` has contributor workflow details.

### Key gotchas

- **CXX=g++ is required.** The default `c++` on this VM is clang-18, which cannot find libstdc++ headers. You must set `export CXX=g++` before running any `cargo build` / `cargo check` / `cargo test` commands, or add it to your shell environment.
- **libstdc++ linker symlink.** The linker also needs `/usr/lib/x86_64-linux-gnu/libstdc++.so` which may not exist by default; create it: `sudo ln -sf /usr/lib/gcc/x86_64-linux-gnu/13/libstdc++.so /usr/lib/x86_64-linux-gnu/libstdc++.so`.
- **Git submodules must be initialized.** Vendored C/C++ deps (freetype, harfbuzz, libpng, zlib) live in `deps/` as git submodules. Run `git submodule update --init --recursive` if they are missing.
- **Rust edition 2024.** Some crates use `edition = "2024"`, which requires Rust >= 1.85. Run `rustup update stable && rustup default stable` to get a compatible toolchain.
- **GUI testing.** `wezterm-gui` requires an X11 display (`DISPLAY=:1`). It uses software rendering (tiny-skia) when no GPU is available and will emit EGL/DRI3 warnings—these are harmless.
- **Headless testing.** `wezterm-mux-server` runs without a display and exposes panes via `wezterm cli` subcommands. Use `--no-paste` with `wezterm cli send-text` to execute commands in panes.
- **Test runner.** `cargo nextest run` is the preferred test runner (see `Makefile`). The `wezterm-escape-parser` crate is `no_std` by default and needs a separate test invocation: `cargo nextest run -p wezterm-escape-parser`.
