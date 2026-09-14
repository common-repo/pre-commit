# common-repo/pre-commit

Source template distributing baseline pre-commit hooks and a PR workflow.

- `src/` is the distributed payload: `.pre-commit-config.yaml` and `.github/workflows/pre-commit.yaml` are renamed into consumer roots by `.common-repo.yaml`; do not place agent indexes under `src/`.
- Root `.github/workflows/`, `.pre-commit-config.yaml`, and `cog.toml` maintain this template's CI, release, and **Cocogitto** commit checks; `.releaserc.yaml` is legacy release configuration. `llms.txt` is repository documentation.
- `.common-repo.yaml` consumes `upstream` for maintenance workflows, then appends this template's pre-commit arrays with `array_mode: append_unique`; downstream additions follow the same merge convention (see `README.md`).
- Validate with `common-repo validate && common-repo apply --dry-run`; run `prek install` on new checkouts/worktrees, then `prek run --all-files`. The distributed config requires `prek >= 0.3.8` and excludes root agent files from rewriting hooks; validation hooks still check them.

## Maintaining this index

- Update the affected `AGENTS.md` files in the same change when paths, responsibilities, commands, dependencies, or conventions change.
- Keep indexes brief: record semantic entry points and non-obvious constraints; link to existing documentation instead of duplicating it.
- Add a directory index only when it provides useful navigation beyond its parent; omit generated, vendored, and fixture trees.
- Every `AGENTS.md` must have a sibling `CLAUDE.md` containing only `@AGENTS.md`.
