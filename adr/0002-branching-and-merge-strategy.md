# 0002. Branch per piece of work, squash-merge via PR, tag each phase

- **Status:** Accepted
- **Date:** 2026-11-22 (recorded 2026-11-24)

## Context

This is a solo learning repo, but it is also a public portfolio. I want `master` to read like a clean log of what I built and learned, and I want practice with the same pull-request workflow I use at work. At the same time, a solo repo shouldn't carry process that slows me down.

The repo also gets direct commits from outside the PR flow: an iPhone Shortcut appends entries to `notes/til.md` through the GitHub API.

## Decision

1. **One branch per piece of work**, named `<area>/<short-name>` where `<area>` matches the top-level folder, for example `notes/00-cluster`, `clusters/nodeport-mapping`, `projects/spire-kind`.
2. **Merge through a pull request with squash-merge only.** Each PR becomes one commit on `master`, and the PR title becomes that commit's subject (see [ADR 0003](0003-commit-message-convention.md)). Branch commits can be messy.
3. **GitHub ruleset on `master`** with these rules:
   - *Restrict updates*: only bypass actors can push to or merge into `master`.
   - *Restrict deletions*: `master` can't be deleted.
   - *Block force pushes*: history on `master` can't be rewritten.
   - *Require linear history*: no merge commits on `master`, which matches squash-only merging.

   *Repository admin* is the only bypass actor, set to *Always*. The bypass lets the iPhone Shortcut (and me, in an emergency) write to `master` directly. *Require a pull request before merging* is deliberately left off: with the bypass in place it adds nothing, and without the bypass it would break the Shortcut.
4. **Annotated tag at the end of each phase**, for example `phase-0`, with a short summary of what the phase produced. GitHub release notes are generated from the PRs merged since the previous tag.

Repository settings that go with this:

- Settings → General → Pull Requests: allow squash merging only (merge commits and rebase merging off); default commit message *Pull request title and description*.
- Settings → General → Pull Requests: *Always suggest updating pull request branches* and *Automatically delete head branches* on. *Allow auto-merge* stays off until there are CI checks to wait for.
- The merge settings can be applied from the CLI so they're reproducible:

  ```bash
  gh repo edit <owner>/ai-sec-lab --enable-merge-commit=false --enable-rebase-merge=false \
    --enable-squash-merge --delete-branch-on-merge --allow-update-branch
  gh api -X PATCH repos/<owner>/ai-sec-lab \
    -f squash_merge_commit_title=PR_TITLE -f squash_merge_commit_message=PR_BODY
  ```

## Alternatives considered

- **Commit straight to `master`:** fastest, but the history becomes noisy work-in-progress and I don't practise the PR workflow.
- **Merge commits (no squash):** keeps every branch commit on `master`. That's useful when individual commits matter, but in a notes-heavy repo they mostly read like "fix typo".
- **Rebase-merge:** linear history like squash, but keeps every branch commit, which leads to the same noise.
- **Branch protection requiring reviews or status checks:** there's no second reviewer, and no CI yet worth gating on.

## Consequences

- `git log --oneline master` shows one line per piece of work, which is easy to scan and feeds release notes per phase.
- Branch-level detail (individual commits) is lost on `master` after the squash. If that history matters for a change, it belongs in the PR description, which GitHub keeps.
- **The ruleset doesn't stop me.** I'm the repository admin, and the bypass is set to *Always*, so none of the four rules apply to my own pushes. They block other tokens and collaborators, but for my own changes the workflow relies on habit. That's a deliberate trade-off to keep the iPhone Shortcut working.
- If CI is added later (for example `kubeconform` or `opa test` in Phase 1+), revisit this: add a required status check, and consider giving the Shortcut its own narrowly scoped token with bypass, then setting the admin bypass to *For pull requests only*.