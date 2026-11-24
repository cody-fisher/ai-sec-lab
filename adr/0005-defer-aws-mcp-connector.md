# 0005. Don't give AI tools AWS access until a lab-only, least-privilege role exists

- **Status:** Accepted
- **Date:** 2026-11-23 (recorded 2026-11-24)

## Context

The AWS MCP server lets an AI assistant run AWS CLI commands and scripts on my behalf. It would speed up Phase 1 and Phase 2 AWS work.

It would also be an example of the exact risk this lab studies. An AI agent holding my personal AWS credentials has whatever access those credentials have. A prompt injection (from a web page, a document or tool output) or a plain mistake could then create, change or delete real resources. The OWASP Top 10 for Agentic Applications lists this kind of excessive agency and tool misuse as a core risk. It would be a bad look for a workload-identity lab to start with an agent running on long-lived human credentials.

Phase 0 doesn't need AWS automation. The only AWS work is ANS-C01 study.

## Decision

Don't use the AWS MCP connector (or any AI tool with AWS access) until Phase 1. Before enabling it:

1. Create a **lab-only IAM role** in a separate lab account (or at minimum a dedicated role in a sandbox account), assumed with short-lived credentials (IAM Identity Center or `aws login`), not static access keys.
2. Scope the role to the services the current phase needs, with a permissions boundary and an explicit deny on IAM user and access-key creation, Organizations and billing.
3. Turn on CloudTrail in that account, so every action the AI takes can be traced afterwards.
4. Record the role's policy and the setup in a new ADR that supersedes this one.

## Alternatives considered

- **Connect it now with my admin profile:** convenient, but it means unlimited blast radius, and it contradicts the point of the lab.
- **Never use AI tools with AWS:** safe, but it gives up the chance to study an agent with cloud access in a controlled way, which is a real Phase 4 topic.

## Consequences

- AWS work in Phase 0 is done by hand with the CLI or console.
- Setting up the lab role becomes a small Phase 1 task, and it doubles as SCS-C03 practice (permission boundaries, SCPs, CloudTrail).
- The local `aws-mcp` server configured in the Claude desktop app should stay disconnected, or its credentials expired, until then.
- Later, the setup gives a concrete before and after for the Phase 4 write-up: an agent with a human's credentials vs one with a scoped workload identity.
