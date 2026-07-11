---
title: harrow
---

**harrow is a formatter for PostgreSQL SQL.** It reads SQL, lays it out in a canonical style, and — because every rendering is verified against the original by [`gomatic/go-sql`](https://github.com/gomatic/go-sql) — never changes what a statement means and never drops a comment.

## What makes it different

- **Meaning-preserving by construction.** Each formatted result is verified against the original; anything harrow can't prove faithful is left exactly as written, so comments and semantics survive.
- **Single verb, pipe-friendly.** With no files it reads standard input and writes to standard output, so it drops straight into a shell pipeline.
- **In-place or check mode.** `--write` rewrites changed files in place; `--list` reports which files would change without touching them — the shape a pre-commit or CI check wants.
- **Only rewrites what changed.** In `--write` mode an already-canonical file is left untouched, so its modification time is preserved.

## Usage

Format standard input to standard output:

```sh
cat schema.sql | harrow
```

Format named files, printing each to standard output:

```text
harrow schema.sql migrations.sql
```

Rewrite changed files in place:

```text
harrow --write *.sql
```

List the paths whose formatting would change (nothing is written):

```text
harrow --list *.sql
```

### Flags

- `--write`, `-w` (`HARROW_WRITE`) — rewrite each changed file in place instead of writing to standard output.
- `--list`, `-l` (`HARROW_LIST`) — print the paths of files whose formatting would change.
- `--log-level` (`HARROW_LOG_LEVEL`, default `warn`) — logging level: `debug`, `info`, `warn`, `error`.
- `--log-format` (`HARROW_LOG_FORMAT`, default `text`) — log output format: `text` or `json`.

## How it works

1. **Read** — harrow reads SQL from each named file, or from standard input when no files are given.
2. **Format** — the SQL is rendered into harrow's canonical layout through [`gomatic/go-sql`](https://github.com/gomatic/go-sql)'s formatter, which verifies the rendering against the input.
3. **Route** — the result goes to standard output by default, back into the file with `--write`, or to a list of changed paths with `--list`.
4. **Fail loud** — a file that won't parse stops the run rather than emitting anything unfaithful.

## Install

```sh
go install github.com/sqlrest/harrow/cmd/harrow@latest
```
