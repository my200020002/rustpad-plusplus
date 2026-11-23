# Contributing

Bug reports, ideas, translations and pull requests are welcome. For bigger
changes, please open an issue first so we can talk about the approach.

## Reporting a bug

Open an [issue](https://github.com/srcmkr/rustpad-plusplus/issues). Please
mention the version (*Help → About rustpad++*), your distribution and desktop,
and, if it is about a file, its size and encoding (shown in the status bar).

## Building from source

You need [Rust](https://rustup.rs/) 1.95 or newer and a few libraries. On
Debian, Ubuntu and Linux Mint:

```sh
sudo apt install build-essential libxkbcommon-x11-dev libxcb1-dev \
    libwayland-dev libfontconfig-dev libvulkan1
git clone https://github.com/srcmkr/rustpad-plusplus
cd rustpad-plusplus
cargo build --release
./target/release/rustpad
```

A copy run from `target/release` adds itself to the application menu of your
user.

Before a pull request, run both of these; they should pass without warnings:

```sh
cargo test --workspace
cargo clippy --workspace --all-targets
```

After changing dependencies, also run `cargo deny check` (install it with
`cargo install --locked cargo-deny`). It checks for known vulnerabilities,
licenses and sources as configured in [`deny.toml`](deny.toml); CI runs it
on every push, and a release waits for it.

## Translations

Texts are gettext catalogs in [`po/`](po): English is written in the code,
every other language is a `.po` file. To fix a translation, edit the
`msgstr`. To add a language, copy `po/rustpad.pot` to `po/<lang>.po`,
translate it (Poedit, Weblate or any text editor) and add it to `CATALOGS` in
[`src/i18n.rs`](src/i18n.rs). `cargo test` fails while a catalog misses a
text or keeps one the code no longer has; after changing texts in the code,
regenerate the template with `cargo test -- --ignored write_pot`.

The Spanish, French, Italian, Brazilian Portuguese and Simplified Chinese
catalogs are drafts that no native speaker has reviewed yet (their first line
says so). A review
of one of them, even of a few strings, is a very welcome contribution; once a
catalog is reviewed, drop the note from its first line.

## Where things are

| Path | Contents |
|---|---|
| [`src/doc`](src/doc) | The document: memory-mapped piece table, line index, rows and wrapping, encodings, type detection |
| [`src/views`](src/views) | Text, hex, JSON tree and Markdown views |
| [`src`](src) | Window, menus, dialogs, settings, session, translations, how the program is installed (`install.rs`) |
| [`packaging`](packaging) | Package build |
| [`patches`](patches) | Patched copies of dependencies, mostly of GPUI for a faster start; the reasons are noted in `Cargo.toml` |
| [`assets`](assets) | Logo, icons, menu entry template |
| [`po`](po) | Translations |
| [`.github/workflows`](.github/workflows) | Tests for every push, releases for every tag |
| [`dev`](dev) | The release script |
| [`CHANGELOG.md`](CHANGELOG.md) | What changed in each version; shown in the app and used as release notes |

## Releases

`dev/release.sh 0.2.0` sets the version, runs the tests, commits, tags and
pushes. The tag starts the release workflow: it tests, builds on Ubuntu 22.04
(so the program runs on older systems too), packs the archive, the Debian
package and the AppImage with `packaging/build-packages.sh` and publishes
them as the latest release.
