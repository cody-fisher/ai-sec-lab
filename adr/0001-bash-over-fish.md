# 0001. Use bash instead of fish for lab work

- **Status:** Accepted
- **Date:** 2026-11-22 (recorded 2026-11-24)

## Context

I prefer fish for day-to-day use: autosuggestions, nicer syntax and good defaults. But the CKA exam (Phase 2) and the CKS exam after it run in a bash terminal on a remote machine. Speed in those exams comes from muscle memory: `alias k=kubectl`, tab completion for `k`, the `$do` dry-run shortcut, and being able to write quick shell loops without thinking about syntax.

Practising daily in one shell and sitting the exam in another means relearning habits under time pressure. fish's syntax is also not POSIX, so one-liners I get used to in fish (`set -x`, `(cmd)` substitution, `and`/`or`) would fail in the exam terminal.

## Decision

Use bash as the lab shell in WSL, with:

- Starship for the prompt (git branch and kube context visible).
- The CKA shortcuts in `~/.bashrc`: `alias k=kubectl`, `complete -o default -F __start_kubectl k`, `export do="--dry-run=client -o yaml"`.
- `git-aliases.bash`, a bash port of the fish `plugin-git` aliases I already use, so git habits carry over.

## Alternatives considered

- **fish everywhere:** best day-to-day experience, but it trains the wrong habits for the exams.
- **zsh with Oh My Zsh:** closer to bash syntax, but still a different completion and config model, and not what the exam provides.
- **fish for general use, bash only for Kubernetes practice:** switching shells by task makes it too easy to drift back to fish habits.

## Consequences

- Exam-style habits get practised every session, not just during exam prep.
- I lose fish's autosuggestions. If I miss them, `ble.sh` can add similar behaviour to bash later.
- Any shell config or helper script I write for the lab must work in plain bash.
- Revisit after the CKS exam. Once the exams are done, the main reason for this decision goes away.
