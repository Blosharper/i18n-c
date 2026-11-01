# Contributing

Thanks for looking. l10n-c edits locale files safely across every locale, from a CLI an
agent can drive and from a Vite translation manager. [README.md](README.md) is the
user-facing contract, and [DEVELOPMENT.md](DEVELOPMENT.md) holds the maintainer notes
on verification and parity.

## Issues first

Open an issue before a substantial change, so the decision and its reasons are written
down before the code. A good defect report names:

1. the versions of `l10n-c`, `vite-plugin-l10n-c`, Node and, for the translation
   manager, Vite and the browser;
2. your configuration file, with private paths replaced by placeholders;
3. the exact command, its output, verbatim, and what you expected;
4. a small set of locale files that reproduces it.

⚠️ **Do not post unreleased text or private product strings.** This repository is
public. Reduce the case to a few made-up keys.

## Before you open a pull request

The packages need Node 22.12 or later. CI's `test` job runs these commands on Node 22:

```bash
npm ci
npm run verify
npm pack --dry-run --workspaces
```

`npm run verify` builds both packages, typechecks them and runs the tests. To run one
step, use `npm run build`, `npm run typecheck` or `npm test`. The deeper tarball and
HTTP checks in `scripts/verify.mjs` are described in
[DEVELOPMENT.md](DEVELOPMENT.md#verification).

## What a change has to clear

- **Writes must not lose data.** A rename refuses cross-domain moves and checks for
  collisions before it writes. Any new write path needs the same care, and a test
  that proves it.
- **Output order is deterministic.** The base locale comes first, then sorted locale
  codes and file names. See
  [Checks and compatibility](README.md#checks-and-compatibility).
- **Tests edit temporary copies,** never a checked-in fixture or a real project.
- **DOM tests do not prove layout.** Say so when a user interface change has only a DOM
  test, and describe how you checked it in a browser.
- **The CLI and the configuration schema are public.** A change to a flag, a message
  that scripts may read, or a configuration key updates the README and
  `packages/l10n-c/schema.json` where it applies, and has a test.

## Commits and pull requests

[Conventional Commits](https://www.conventionalcommits.org/), with a scope where one
fits: `feat(cli):`, `fix(vite-plugin):`, `ci(release):`. Subject and body in English,
one unit of work per commit.

Every change reaches `main` as a pull request and is **squash-merged**, so:

- **The pull request title is the commit subject on `main`** and must itself be a
  Conventional Commit subject. For a single-commit branch, GitHub uses that commit's
  subject, so keep the two the same. release-please reads the subject to pick the next
  version: `feat` bumps the minor, `fix` and `perf` the patch. While a package is below
  1.0.0, a breaking change (`!` or a `BREAKING CHANGE:` footer) bumps the minor. Other
  types do not cut a release on their own. See [RELEASING.md](RELEASING.md).
- **The squash body is made of the branch's commit messages**, not of the pull request
  description. Footers that must reach `main`, such as `Closes #N` or
  `BREAKING CHANGE:`, go in a commit message.
- **The required check is `test`.** It has to be green before the merge.

## Licence

Contributions are accepted under the MIT licence in [LICENSE](LICENSE).
