<p align="center">
    <a href="https://github.com/lupaxa-git-toolbox">
        <img src="https://raw.githubusercontent.com/the-lupaxa-project/brand-assets/master/logos/organisations/git-toolbox/readme-logo.png" alt="Organisation Logo" />
    </a>
</p>

<h1 align="center">Git Delete Tags</h1>

Delete local and remote Git tags with dry-run, summary, and protection.

## What it Does

`git-delete-tags` removes one or more tags from a repository — locally, on a remote, or both. You can name specific tags, pass `all` to target every tag, and protect selected names from deletion.

Use it when cleaning up stale release tags, resetting tag namespaces before a fresh release cycle, or removing tags that should never have been pushed.

## Install

With Homebrew:

```bash
brew tap the-lupaxa-project/tap
brew trust the-lupaxa-project/tap
brew install git-delete-tags
```

Or run the script from a clone: `./src/git-delete-tags --help`.

## Quick Start

```bash
./src/git-delete-tags all --summary      # plan only — no changes
./src/git-delete-tags -n v1.0.0          # dry-run (commands simulated)
./src/git-delete-tags v1.0.0             # delete one tag (local + remote)
./src/git-delete-tags all -y             # delete all tags (non-interactive)
```

**This is destructive.** Prefer `--summary` or `-n` first.

## Default Behaviour

With no mode flags, the script will:

1. Resolve requested tags (explicit names or `all`)
2. Drop protected tags (`-T` / `--protect-tag`)
3. Delete matching tags on the remote (one `git push --delete` per tag)
4. Delete matching tags locally (one `git tag -d` per tag)

Remote tag names are discovered via `git ls-remote --tags`.

## Common Options

| Flag                     | Purpose                                              |
| :----------------------- | :--------------------------------------------------- |
| `-r, --remote NAME`      | Remote to operate on (default: `origin`)             |
| `--local-only`           | Delete only local tags (no remote operations)        |
| `--remote-only`          | Delete only remote tags (keep local tags)            |
| `-T, --protect-tag TAG`  | Protect a specific tag from deletion (repeatable)    |
| `-y, --yes`              | Skip the interactive `DELETE` confirmation           |
| `-n, --dry-run`          | Simulate commands without changing anything          |
| `-S, --summary`          | Print a detailed plan based on all options and exit  |
| `-V, --version`          | Print version and exit                               |

```bash
./src/git-delete-tags --help
```

## Examples

Preview what deleting all tags would do:

```bash
./src/git-delete-tags all --summary
```

Dry-run a single tag delete:

```bash
./src/git-delete-tags v2.0.0 --dry-run
```

Delete all tags except protected ones:

```bash
./src/git-delete-tags all -T v1.0.0 -T latest --yes
```

Remove tags from the remote only:

```bash
./src/git-delete-tags all --remote-only --yes
```

## Safety Notes

- `--dry-run` prints the action plan and simulates commands without asking for `DELETE`.
- Real runs require typing `DELETE` unless you pass `-y` / `--yes`.
- `--summary` resolves tags and prints the plan only — no deletes are run.
- Protection is applied before existence checks, so a missing explicit tag that is also protected (for example, `no-such -T no-such`) is skipped and may exit 0.
- Explicit tag names: the script fails if a named tag is missing from every place the selected mode would touch (default: local and/or remote; `--local-only`: local; `--remote-only`: remote).
- With `all`, only tags that exist for the selected mode are deleted; the script may exit 0 with nothing to delete.
- Remote tag discovery uses `git ls-remote --tags`; a missing or unreachable remote fails before any deletes.
- Remote deletes run before local deletes; a remote failure aborts before local tags are touched.

<a href="https://github.com/the-lupaxa-project">
    <img src="https://raw.githubusercontent.com/the-lupaxa-project/brand-assets/master/logos/components/footer-for-child-orgs.svg" alt="The Lupaxa Project Footer" width="100%" />
</a>
