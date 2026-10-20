# Kitty Unicode placeholders in this fork

The fork's `main` combines current upstream with the pane-focus fix and
upstream PR #5531 (also retained on the `kitty-unicode-placeholders` bookmark).
It interprets Kitty `U=1` virtual placements and placeholder
cells, then sends ordinary cropped Kitty placements to the outer terminal.
Applications can therefore compose images as cells: scrolling, overwriting and
popup occlusion follow the terminal grid instead of leaving a floating image.

The server sets `ZELLIJ_KITTY_UNICODE_PLACEHOLDERS=1` in child panes to advertise
this interpreter to Neohelix. This is a fork-specific capability marker, not an
upstream Zellij setting. It does not imply that the outer terminal supports
graphics; Kitty graphics must also be enabled and supported by the host terminal.
Restart the server after upgrading, since a new client cannot upgrade an existing
server. Do not manually set the marker on an unpatched server.

Virtual-placement scaling rejects zero cell sizes, overflowing dimensions,
dimensions above 32767 cells per axis, and RGBA rasters over 64 MiB
before allocation. Only `U=0` and `U=1` are accepted.

Placeholder rasters use distinct aspect-fit cache keys, so they cannot alias
ordinary stretched image placements. Host transfers retain upstream's local
media path and capability-negotiated zlib compression; compressed PNG input
must not unconditionally become megabytes of uncompressed terminal output.

Regression tests cover Neohelix's separate PNG upload and virtual placement,
explicit row/column diacritics, offsets, resize, scroll, cell overwrites, deletion
and excessive dimensions, alongside the existing Kitty parser/output tests:

```sh
cargo test --locked -p zellij-server kitty --no-default-features -- --test-threads=2
```

Source: https://github.com/zellij-org/zellij/pull/5531
