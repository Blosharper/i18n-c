---
name: l10n-c
description: Search and edit locale files with the l10n-c CLI instead of editing JSON by hand. Use for any translation task in a project with l10n-c installed - finding a string, adding or updating keys, renaming or deleting keys across languages, replacing text, and checking missing, unused, duplicate or misplaced keys.
---

# l10n-c

Use this CLI for locale files. Do not open or edit them directly. Direct edits risk
JSON syntax errors, keys that drift between languages, and large files in your context.

## Before you start

- Run commands from the directory that holds `l10n-c.config.json`. The CLI does not
  search parent directories.
- Without a configuration file, the defaults apply: `public/locales`, base language
  `ko`, source path `src`.
- Read the configuration when you need `localesDir`, `baseLanguage` or `languageOrder`.
- Use the language codes that already exist as directories under `localesDir`. A new
  code creates a new language folder, so add one only when asked.

## Data model

Each language is a directory, and each JSON file in it is a domain. Keys are flattened
with dots:

```text
messages/en/ui.json    { "ui": { "start": "Start" } }    ->    ui.start
```

The first key segment names the domain, so `shop.buy` lives in `shop.json`. Array items
use numeric segments, such as `ui.items.0`. Values written by the tools must be
strings.

## Commands

Search before you add a key, so you reuse existing strings and naming.

```sh
npx l10n-c --search start                       # keys and values, case insensitive
npx l10n-c --search start --file ui             # limit any operation to one domain
npx l10n-c --update 'en:ui.start=Start' --update 'fr:ui.start=Démarrer'
npx l10n-c --publish '{"en":{"ui.start":"Start"},"fr":{"ui.start":"Démarrer"}}'
npx l10n-c --rename 'ui.old=ui.new'             # key or subtree, every language
npx l10n-c --delete 'ui.obsolete'               # key or subtree, every language
npx l10n-c --delete 'fr:ui.obsolete'            # one language only
npx l10n-c --replace-text 'en:Old=New'          # text inside values, one language
```

Checks:

```sh
npx l10n-c --missing          # against the configured base language
npx l10n-c --missing=fr       # against another language
npx l10n-c --unused           # heuristic scan of the configured source paths
npx l10n-c --nest-check       # keys in the wrong file
npx l10n-c --duplicate-check  # duplicate keys and repeated values
```

Run `npx l10n-c --help` for the full list.

## Rules that prevent mistakes

- **Read the exit code and the output.** `--rename`, `--delete` and `--replace-text`
  exit 1 when nothing matches. Check the key with `--search` instead of retrying.
- **A zero exit does not mean a clean check.** `--missing` and repeated values are
  advisory and exit 0, so read the list. Invalid nesting and duplicate keys exit 1.
- **Watch for `new key ... does not exist in other languages`.** This warning after
  `--update` often means a misspelled key. If you meant to create the key, add the
  other languages too.
- **`--rename` stays inside one domain.** For a move across domains, run `--update` for
  each language's new key, then `--delete` the old key.
- **Use `--publish` for multi-line values.** `--update` cannot carry a real newline, and
  a typed `\n` is stored as a literal backslash and `n`. JSON `\n` in `--publish` is a
  real newline.
- **`--unused` needs review.** Keys built at runtime may not be detected. Treat
  `Potentially Dynamically Constructed Keys` as unknown, and do not delete them on this
  result alone.
- **Writes across files are sequential.** If one fails partway, earlier files may
  already have changed. Check `git diff` before you retry.

## Finish

Report what the output said: keys created and updated, entries changed, and anything
still missing. Then show the locale changes with `git diff`.
