# 0003. PR titles use `area(scope): summary`; branch commits are free-form

- **Status:** Accepted
- **Date:** 2026-11-24

## Context

With squash-merge ([ADR 0002](0002-branching-and-merge-strategy.md)), the PR title becomes the commit on `master`, and the branch and its commits disappear after merge. So the branch name (`notes/00-cluster`) is not visible in `git log` on `master`. The commit subject is the only label left.

I want to scan the history and group it by area at the end of each phase, without spending effort polishing commits that will be squashed away anyway.

## Decision

**PR titles** (and therefore commits on `master`) use:

```
<area>(<optional scope>): <summary in imperative mood>
```

- `<area>` is the top-level folder the change lives in: `notes`, `clusters`, `projects`, `console`, `adr`. Use `docs` for the README and `chore` for repo housekeeping.
- `(<scope>)` is optional. Use it only when an area holds several distinct things, for example `projects(spire)` vs `projects(agentgateway)`. For `notes`, the scope usually adds nothing, because `git log -- notes/00-cluster.md` finds those commits anyway.
- Summary: lowercase, imperative ("describe…", "add…"), no trailing period, around 70 characters or fewer.

Examples:

```
notes: describe what each system component does in the kind cluster
clusters: map NodePort 30080 to the host
projects(spire): deploy SPIRE server and agent on kind
adr: record branching and commit conventions
```

**Branch commits** are free-form. `add experiment results` is fine.

**Commit or PR bodies** are written only when they record something the diff can't show: why a value was chosen, what was tried and rejected, side effects or follow-ups. They aren't written to restate what changed.

## Alternatives considered

- **Conventional Commits types (`feat`, `fix`, `docs`, …):** a widely used standard, but those types describe changes to a product. Most changes here are notes, cluster config and experiments, so nearly everything would be `docs` or `feat` and the type would carry no information. Folder names map to how the repo is organized.
- **No convention:** fine for a few commits, but hard to scan or group once there are hundreds across six phases.
- **Same convention on every branch commit:** extra effort for commits that get squashed away.

## Consequences

- `git log --oneline master` can be scanned or filtered by area: `git log --oneline --grep '^projects'`.
- Release notes per phase can be grouped by area. Tools like `git-cliff` or `commitlint` would need custom type lists, because these aren't the standard Conventional Commits types.
- The convention is enforced by habit only. If it drifts, a PR title check in CI is a cheap fix.
