# Identity fundamentals (identity-labs)

Hands-on work behind the roadmap. Projects built in this repo live in the folders below;
learning projects built in separate repos are linked from the table
([ADR 0006](../../adr/0006-learning-repos-private.md)).

Some linked repos are private while I'm working in them. Each row should make sense
without opening the link.

## Identity fundamentals (`identity-labs`)

Small, local implementations of the identity building blocks behind workload identity
and agent delegation. Built in order; each reuses the one before.

| # | Project | What it shows | Key insight | Status | Code |
| --- | --- | --- | --- | --- | --- |
| 01 | mTLS from scratch | My own root CA with `openssl`, a Go HTTPS server that requires client certificates, and four broken-cert experiments (no cert, wrong CA, expired, wrong hostname) | _TODO: one sentence, written after the experiments_ | 🟡 In progress | [identity-labs/01-mtls](https://github.com/<you>/identity-labs/tree/master/01-mtls) |
| 02 | JWT by hand | | | ⚪ Not started | |
| 03 | OAuth 2.0 and OIDC | | | ⚪ Not started | |
| 04 | SPIRE on kind | | | ⚪ Not started | |
| 05 | RFC 8693 token exchange | | | ⚪ Not started | |

### 01 · mTLS from scratch

**Why it matters for this roadmap.** Every later phase relies on mTLS: SPIRE issues
X.509-SVIDs for it (Phase 1), Istio ambient's ztunnel uses it between pods (Phase 3), and
agentgateway checks identities from it (Phase 3–4). Doing it by hand first means I can
read those components' errors.

**What I broke and what happened** (full table in the project README):

| Experiment | Error I got | What it means |
| --- | --- | --- |
| No client cert | _TODO_ | _TODO_ |
| Client cert from a second CA | _TODO_ | _TODO_ |
| Expired client cert | _TODO_ | _TODO_ |
| Wrong hostname | _TODO_ | _TODO_ |

**Connects to:** [notes/00-cluster.md](../../notes/00-cluster.md) (Phase 0),
SPIRE on kind (Phase 1), Istio ambient (Phase 3).
