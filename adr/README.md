# Architecture decision records

Each file records one decision: the context, what I chose, what else I considered, and what follows from it. ADRs aren't edited after they're accepted. When a decision changes, a new ADR supersedes the old one, and the old one's status is updated to point at it.

| # | Decision | Status |
| --- | --- | --- |
| [0001](0001-bash-over-fish.md) | Use bash instead of fish for lab work | Accepted |
| [0002](0002-branching-and-merge-strategy.md) | Branch per piece of work, squash-merge via PR, tag each phase | Accepted |
| [0003](0003-commit-message-convention.md) | PR titles use `area(scope): summary`; branch commits are free-form | Accepted |
| [0004](0004-kind-for-local-cluster.md) | Use kind for the local Kubernetes cluster | Accepted |
| [0005](0005-defer-aws-mcp-connector.md) | Don't give AI tools AWS access until a lab-only, least-privilege role exists | Accepted |

## Template

Copy this into `adr/NNNN-short-title.md`:

```markdown
# NNNN. Decision in a few words

- **Status:** Proposed | Accepted | Deprecated | Superseded by [NNNN](NNNN-....md)
- **Date:** YYYY-MM-DD

## Context
What situation or problem forced a choice? What constraints apply?

## Decision
What I chose, stated plainly.

## Alternatives considered
- **Option:** why not.

## Consequences
What gets easier, what gets harder, what to revisit and when.
```
