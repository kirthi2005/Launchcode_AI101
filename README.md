# Launchcode_AI101
# AI Dev Assistant — Enterprise Automation Architecture

An AI-driven agentic workflow built in Zapier that transforms fragmented engineering data (Jira, GitHub, GitHub Actions) into a structured, decision-ready daily operating brief — complete with standup scripts, blocker analysis, prioritized recommendations, and human-approved Slack distribution.

## Problem

Developers lose significant time each day gathering information from Jira, GitHub, GitHub Actions, and Slack to prepare standups, prioritize work, track blockers, monitor deployments, and review team performance. This fragmented workflow creates constant context switching and delays effective decision-making.

## Solution

AI Dev Assistant aggregates and normalizes engineering data, uses AI (GPT-4o) to analyze it, and produces a consolidated daily report — delivered through existing tools (Google Docs, Slack) with a human approval gate before distribution.

- Aggregates data from Jira, GitHub PRs, and GitHub Actions in parallel
- Normalizes and categorizes work items by status, priority, blockers, and due dates
- Analyzes pull requests, code reviews, deployments, and team velocity
- Uses AI to generate recommendations, risk signals, and prioritized action items
- Delivers a 2-minute standup script and engineering dashboard via Google Docs and Slack
- Implements a reusable intelligence layer, not a one-off automation

## Architecture

**11-step Zapier workflow**, triggered daily at 7:00 AM UTC (weekdays):

1. **Trigger** — Scheduled Zapier trigger (cron, weekdays only)
2. **Data Aggregation (parallel)** — Jira ticket query (JQL), GitHub PR query (open/merged/large changes), GitHub Actions build & deployment status
3. **Validation & Retry Logic** — Code by Zapier validates and retries each API call independently
4. **Format & Categorize** — Priority sorting and metadata normalization
5. **AI Summary** — GPT-4o mini synthesizes 16 input fields into a standup script, daily summary, recommendations, and resource links
6. **Output Validation** — Checks AI output formatting and completeness before distribution
7. **Google Docs Archive** — Full summary appended to a dedicated archive doc
8. **Human Approval Gate** — Slack-based human-in-the-loop review (3-hour timeout)
9. **Slack Distribution** — Approved summary posted to `#stand_up_summary`; failures routed to `#alert_channel` with email fallback

**Performance:** Parallel API calls reduce aggregation time from 6+ seconds to ~3 seconds. Total workflow runtime: 15–20 seconds end-to-end (before human approval).

## Tech Stack

- **Orchestration:** Zapier (Schedule, Code by Zapier, AI by Zapier, Human in the Loop)
- **AI:** GPT-4o / GPT-4o mini (OpenAI)
- **Data Sources:** Jira Cloud API v3 (JQL), GitHub REST API v3
- **Distribution:** Slack API, Google Docs API
- **Validation:** Custom JavaScript (Code by Zapier) for retry logic, error handling, and output validation

## Security & Governance

- **Scoped, least-privilege access** — Jira read-only (find issues), Google Drive limited to a single "Jira Summary" doc, Slack limited to three named channels (`stand_up_summary`, `approval_summary`, `alert_channel`)
- **Mandatory human approval** before any AI-generated content is distributed — no autonomous output delivery
- **Decision-support only** — AI outputs recommendations, not actions; humans own final accuracy and prioritization
- **Error handling at every step** — Slack alerts on failure with email fallback so no data is ever silently lost

## Why Zap with AI (Architecture Decision)

Evaluated three approaches against a four-lens framework (performance, governance, maintainability, scale):

| Approach | Verdict |
|---|---|
| General-purpose AI agent (manual prompting) | Easy to prototype, but manual, inconsistent, no auditability |
| Fully autonomous Zapier Agent | Strong orchestration, but over-engineered — excessive permissions and maintenance overhead for this use case |
| **Zap with AI (selected)** | Best balance: scheduled & repeatable, scoped governance, human checkpoints, low maintenance |

## Measured Results

| Metric | Before | After |
|---|---|---|
| Daily reporting time | 75 min | ~10 min (incl. human review) |
| Time reduction | — | **87%** |
| Output quality | ~70% usable | **90%+** accepted with minor/no edits |
| Infrastructure cost | $0 | ~$20/mo (~$240/yr) |

**Annual impact:** ~270 engineering hours recovered per year (≈6.75 weeks of capacity), an estimated **$27,000** in recovered labor value at a $100/hr engineering rate — against ~$240/yr in infrastructure cost (100:1+ ROI).

Validated via 3 representative production runs (June 4–6, 2026), each completing in 16–19 seconds with all outputs passing quality review.

## Validation Methodology

Tested without a full build using four prototype methods before committing to the architecture:

- **Prompt prototype** — GPT-4o tested against real 16-field Jira/GitHub data
- **Stubbed test** — Output routed to Google Docs before any Slack distribution
- **Shadow mode** — 3-hour human approval gate comparing AI output against expectations
- **Zero-harm rule** — Scoped permissions + email fallback ensure no data loss on failure

**Decision thresholds:** Quality ≥ 90%, time reduction > 50%, zero undetected errors, positive ROI — all met → **GO**.

## Responsible AI Use

Aligned to five trust fundamentals: scope (limited to repetitive reporting), constraints (incidents/security events escalate to humans), verification (validation checkpoints every cycle), consistency (same review process daily), and accountability (humans own final accuracy and distribution).

---

*Built as a capstone project for AI101 - Launchcode.*
