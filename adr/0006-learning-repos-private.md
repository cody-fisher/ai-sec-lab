# 0006: Learning repos stay private; finished work is linked from ai-sec-lab

| | |
| --- | --- |
| **Status** | Proposed |
| **Date** | 2026-11-24 |
| **Deciders** | Cody |
| **Related** | ADR 0002 (branching and PRs), ADR 0005 (no AI tools with AWS access) |

## Context

`ai-sec-lab` is the repo I show people: the roadmap, cluster notes, ADRs and, later, the
Trust Console. It should stay easy to read.

Some learning happens better in its own repo. `identity-labs` holds five small projects
(mTLS, JWT by hand, OAuth/OIDC with a React client, SPIRE on kind, an RFC 8693 STS).
They are deliberately rough: broken certificates, failing tokens and half-finished notes
are part of the method. Mixing that work into `ai-sec-lab` would bury the polished parts;
making it public too early would show unfinished work first.

I still want anyone reviewing my portfolio, to see the finished
work and to see how I learned it.

The learning repos are built with Claude Code, guided by a `CLAUDE.md` file that sets the
rules for the agent (no AWS, no reading credential files, gitleaks before push).

## Decision

1. **Learning repos start private.** `identity-labs`, and any future repo like it, is
   created private.
2. **Reviewers get read access, not a public repo.** To show work in progress, I add the
   reviewer as a collaborator with `read` permission.
3. **`ai-sec-lab` links to finished projects.** `projects/README.md` has one row per finished
   project: what it shows, its key insight in one sentence, and a link to the project
   folder on `master` (`/tree/master/<folder>`), never to a branch.
4. **A project counts as finished when:**
   - its "Done when" criteria in `CLAUDE.md` are met
   - its README has all five lab-note sections filled in
   - it is merged to `master` through a PR
   - `gitleaks git` is clean
5. **Going public is a separate decision.** Before a learning repo becomes public, I run
   `gitleaks git` over the full history and read through the commit log, because the
   history becomes public too.
6. **AI assistance stays visible.** `CLAUDE.md` is committed, not hidden. The lab notes
   (errors, what I understood, key insights) are written by me.
7. **No submodules and no copies.** The link is plain Markdown. Code lives in one place.

## Consequences

### Easier

- `ai-sec-lab` stays readable; the summary table can be read without opening anything.
- I can experiment and break things without worrying about who sees it.
- Writing the "Key insight" column forces me to reduce each project to one sentence I can
  say in a walkthrough.

### Harder

- While `identity-labs` is private, the links show a 404 to anyone I haven't invited. The
  summary row has to make sense on its own.
- Two repos to keep in step: when a project folder is renamed, its link in `ai-sec-lab`
  breaks. Check links when updating `projects/README.md`.
- Collaborator access has to be managed by hand, and removed when no longer needed.

## Alternatives considered

| Option | Why not |
| --- | --- |
| Do everything in `ai-sec-lab` | Rough learning work would bury the polished parts. |
| Make `identity-labs` public from day one | Unfinished work is seen first; any mistake in history is public immediately. |
| Copy finished projects into `ai-sec-lab/projects/` | Two copies drift apart. |
| Git submodule | Awkward clones, and a private submodule fails for anyone without access. |

## Commands

```bash
# give a reviewer read access to a private learning repo
OWNER=$(gh api user -q .login)
gh api -X PUT repos/$OWNER/identity-labs/collaborators/<their-login> -f permission=read

# remove it later
gh api -X DELETE repos/$OWNER/identity-labs/collaborators/<their-login>

# before making a learning repo public
gitleaks git -v
gh repo edit $OWNER/identity-labs --visibility public --accept-visibility-change-consequences
```
