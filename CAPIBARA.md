TODO: this readme needs polishing

# Capibara Theme

Goal: tweak colors/font/spacing of Lemmy's default "darkly"/"litely" themes.

## Preview setup

```bash
git clone https://github.com/LemmyNet/lemmy-ui --recursive
cd lemmy-ui
pnpm install
LEMMY_UI_BACKEND=https://lemmy.sinistraverso.org pnpm dev
```

Then open `localhost:1234`. This runs your local editable lemmy-ui against a
public test backend, no need to run Postgres/Rust backend/Docker yourself.

## Creating a theme

1. In `src/assets/css/themes/`, copy the base theme's pair of files (e.g.
   `litely.scss` + `_variables.litely.scss`) to new names (`litely-mine.scss`
   + `_variables.litely-mine.scss`).
2. Fix the `@import` in the new `.scss` file to point to the new variables
   file.
3. Edit Bootstrap variables in `_variables.litely-mine.scss` (`$primary`,
   `$body-bg`, `$font-family-base`, `$font-size-base`, `$spacer`,
   `$border-radius`, etc).
4. Run `pnpm themes:build` to compile via Sass once, or see below to watch
   and recompile automatically while you iterate.
5. To actually see it in `/settings` while developing, symlink or copy the
   compiled `.css` into an `extra_themes` folder at the repo root (create it
   if missing). Per the
   [official theming docs](https://join-lemmy.org/docs/administration/theming.html),
   that folder is scanned fresh on every request for the theme list — so the
   new theme shows up immediately, no server restart needed.

## Watching for changes

While iterating on colors/spacing, run Sass in watch mode in a second
terminal so every save recompiles the theme's `.css`:

```bash
pnpm themes:watch
```

This runs `sass --watch src/assets/css/themes/:src/assets/css/themes
--embed-sources` and rebuilds on every `.scss` save. Keep it running
alongside `pnpm dev`.

For `litely-capibara` specifically, use this instead — it compiles straight
into `extra_themes/` so the live-reloaded result shows up in `/settings`
immediately, with no manual copy step:

```bash
pnpm themes:watch:capibara
```

## Distribution

Normally just the compiled `.css` file, dropped into an instance's
`extra_themes` folder (Docker: `./volumes/lemmy-ui/extra_themes`, or set via
`LEMMY_UI_EXTRA_THEMES_FOLDER`) — no lemmy-ui rebuild needed, admin selects
it under `/admin` or users under `/settings`.

Community theme packs (e.g. `2xx04/lemmy-ui-themes`) are just folders of
these ready-to-copy `.css` files, sometimes with a `.diff.css` variant for
the Stylus browser extension (for people previewing on someone else's
instance).

Getting a theme merged upstream into `LemmyNet/lemmy-ui` requires the full
source (`.scss` + `_variables.*.scss`), prettier-formatted, in its own
subfolder.
