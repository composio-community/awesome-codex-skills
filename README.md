<h1 align="center">Awesome Codex Skills</h1>

<p align="center">
<a href="https://dashboard.composio.dev/login?utm_source=Github&utm_medium=Youtube&utm_campaign=2025-11&utm_content=AwesomeCodexSkills">

  <img width="1280" height="640" alt="Composio banner" src="codex_cover_image.png">
</a>
</p>

<p align="center">
  <a href="https://awesome.re">
    <img src="https://awesome.re/badge.svg" alt="Awesome" />
  </a>
  <a href="https://makeapullrequest.com">
    <img src="https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square" alt="PRs Welcome" />
  </a>
</p>
<div>
<p align="center">
  <a href="https://twitter.com/composio">
    <img src="https://img.shields.io/badge/Follow on X-000000?style=for-the-badge&logo=x&logoColor=white" alt="Follow on X" />
  </a>
  <a href="https://www.linkedin.com/company/composiohq/">
    <img src="https://img.shields.io/badge/Follow on LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white" alt="Follow on LinkedIn" />
  </a>
  <a href="https://discord.com/invite/composio">
    <img src="https://img.shields.io/badge/Join our Discord-5865F2?style=for-the-badge&logo=discord&logoColor=white" alt="Join our Discord" />
  </a>
  </p>
</div>

A curated list of practical Codex skills for automating workflows across the Codex CLI and API.

## Give your skills real-world actions

Skills tell your agent **how** to work. An MCP Gateway gives it secure access to the tools it needs.

Composio [MCP Gateway](https://composio.dev/mcp-gateway) provides a single MCP endpoint for 1,000+ integrations with built-in authentication, team-based access controls, audit logs, and production-ready reliability.

---

## Quickstart: Add Skills to Codex

### Install with the Skill Installer (recommended)

```bash
git clone https://github.com/ComposioHQ/awesome-codex-skills.git
cd awesome-codex-skills
# Install one or more skills into $CODEX_HOME/skills (defaults to ~/.codex/skills)
python skill-installer/scripts/install-skill-from-github.py --repo ComposioHQ/awesome-codex-skills --path meeting-notes-and-actions
```

The installer fetches the skill and places it in `$CODEX_HOME/skills/<skill-name>`. Restart Codex to pick up new skills.

### Manual install

1. Copy the desired skill folder (e.g., `./spreadsheet-formula-helper`) into `$CODEX_HOME/skills/` (defaults to `~/.codex/skills/`).
2. Restart Codex so it loads the new metadata.
3. In your next session, describe the task or mention the skill name; Codex will trigger matching skills based on their `description` frontmatter.

---

## Contents

- [Bernstein](https://github.com/chernistry/bernstein) - Multi-agent orchestrator with Codex CLI adapter. Runs parallel Codex agents in isolated git worktrees with quality gates.
- [What Are Codex Skills?](#what-are-codex-skills)
- [Skills](#skills)
  - [Development & Code Tools](#development--code-tools)
  - [Productivity & Collaboration](#productivity--collaboration)
  - [Communication & Writing](#communication--writing)
  - [Data & Analysis](#data--analysis)
  - [Meta & Utilities](#meta--utilities)
- [Using Skills in Codex](#using-skills-in-codex)
- [Creating Skills](#creating-skills)
- [Contributing](#contributing)
- [Join the Community](#join-the-community)

## What Are Codex Skills?

Codex skills are modular instruction bundles that tell Codex how to execute a task the way you want it done. Each skill lives in its own folder with a `SKILL.md` that includes metadata (name + description) and step-by-step guidance. Codex reads the metadata to decide when to trigger a skill and loads the body only after it fires, keeping context lean.

## Skills

### Development & Code Tools

- [brooks-lint](https://github.com/hyhmrright/brooks-lint) - AI code reviews grounded in six classic engineering books — decay risk diagnostics with book citations, severity labels, and four analysis modes (PR review, architecture audit, tech debt, test quality). Install: `python3 ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py --repo hyhmrright/brooks-lint --path skills/brooks-lint --name brooks-lint`
- [bringyour-migration-auditor](https://github.com/unitedideas/bringyour-mcp/tree/main/skills/bringyour-migration-auditor) - Audit Claude Code to Codex harness migrations for AGENTS.md/CLAUDE.md scope, hooks, MCP config, skills, secrets, and validation notes. Install: `python3 ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py --repo unitedideas/bringyour-mcp --path skills/bringyour-migration-auditor --name bringyour-migration-auditor`
- [codebase-migrate/](./codebase-migrate/) - Run large codebase migrations and multi-file refactors in reviewable batches with CI verification.
- [codebase-recon](https://github.com/yujiachen-y/codebase-recon-skill) - Analyze git history to understand a codebase before reading any code — surfaces hotspots, bug magnets, bus factor, momentum, and high-risk files (hotspot ∩ bug-magnet) via auto-scaled analysis. Install: `python3 ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py --repo yujiachen-y/codebase-recon-skill --path skills/codebase-recon --name codebase-recon`
- [create-plan/](./create-plan/) - Quickly draft concise execution plans for coding tasks.
- [deploy-pipeline/](./deploy-pipeline/) - End-to-end Stripe → Supabase → Vercel release pipelines with verify and rollback.
- [Emdash Skills](https://github.com/megabytespace/claude-skills) - 14-category autonomous product-building OS: CF Workers + Hono + Angular + D1 + Stripe. One-line prompts to deployed SaaS with 94 reference docs, 18 agents, and Codex-native `.agents/skills/` support.
- [gh-address-comments/](./gh-address-comments/) - Address review or issue comments on the open GitHub PR for the current branch using `gh`.
- [gh-fix-ci/](./gh-fix-ci/) - Inspect failing GitHub Actions checks, summarize failures, and propose fixes.
- [mcp-builder/](./mcp-builder/) - Build and evaluate MCP servers with best practices and an evaluation harness.
- [pr-review-ci-fix/](./pr-review-ci-fix/) - Automated GitHub/GitLab PR review plus CI auto-fix loop via the Composio CLI.
- [sentry-triage/](./sentry-triage/) - Diagnose Sentry issues by mapping stack frames to local source — no copy-paste.
- [webapp-testing/](./webapp-testing/) - Run targeted web app tests and summarize results.
- [AuraKit](https://github.com/smorky850612/Aurakit) - All-in-one skill framework: 46 modes, 23 sub-agents, 6-layer OWASP security, 10 lifecycle hooks, ~55% token savings. Install: `npx @smorky85/aurakit`
- [Vibe-Skills](https://github.com/foryourhealth111-pixel/Vibe-Skills) - Governed Codex skill harness for staged, test-driven work: routes 340+ skills through requirement freeze, plan approval, execution, verification evidence, and cross-session memory.
- [polywave](https://github.com/blackwell-systems/polywave-codex) - Parallel agent coordination with structural merge safety. Scout decomposes, Wave agents implement in isolated worktrees with disjoint file ownership. Same protocol on Claude Code and Codex.

### Productivity & Collaboration

- [connect/](./connect/) - Connect Codex to 1000+ apps via the Composio CLI for real actions (Slack, GitHub, Notion, etc.).
- [connect-apps/](./connect-apps/) - Wire up Composio CLI connections for Claude and kick off app workflows from the shell.
- [issue-triage/](./issue-triage/) - Triage Linear or Jira backlogs and run bug sweeps from the terminal.
- [linear/](./linear/) - Manage issues, projects, and team workflows in Linear.
- [meeting-insights-analyzer/](./meeting-insights-analyzer/) - Analyze meeting transcripts for themes, risks, and follow-ups.
- [meeting-notes-and-actions/](./meeting-notes-and-actions/) - Turn meeting transcripts into summaries with decisions and owner-tagged action items.
- [internal-comms/](./internal-comms/) - Craft internal announcements, updates, and stakeholder messaging.
- [invoice-organizer/](./invoice-organizer/) - Normalize and extract invoice data for tracking and reporting.
- [notion-knowledge-capture/](./notion-knowledge-capture/) - Convert chats or notes into structured Notion pages with proper linking.
- [notion-meeting-intelligence/](./notion-meeting-intelligence/) - Prepare meeting materials with Notion context plus Codex research.
- [notion-research-documentation/](./notion-research-documentation/) - Synthesize multiple Notion sources into briefs, comparisons, or reports with citations.
- [notion-spec-to-implementation/](./notion-spec-to-implementation/) - Turn Notion specs into implementation plans, tasks, and progress tracking.
- [support-ticket-triage/](./support-ticket-triage/) - Triage customer support tickets with categories, priority, next actions, and draft replies.
- [file-organizer/](./file-organizer/) - Organize, rename, and tidy files to keep workspaces clean.
- [paperjsx/](./paperjsx/) - Generate PPTX presentations, DOCX documents, XLSX spreadsheets, and PDF invoices/reports/charts from structured JSON. Runs locally via `@paperjsx/mcp-server` — no API key, no network calls.
- [skill-share/](./skill-share/) - Share skills and reusable instructions across teammates.
- [Taisly Agent Kit](https://github.com/taisly/agent) - Publish approved short-form videos from Codex with a bundled skill, CLI, and remote MCP server for TikTok, Instagram Reels, YouTube Shorts, X, and Facebook.

### Communication & Writing
- [codex-sms-verification](https://github.com/virtualsms-io/codex-sms-verification) - External repo: real-SIM SMS verification for AI agents via VirtualSMS MCP. 145+ countries, 2000+ services, both hosted (mcp.virtualsms.io) and local stdio transports.

- [email-draft-polish/](./email-draft-polish/) - Draft, rewrite, or condense emails for the right tone and audience.
- [changelog-generator/](./changelog-generator/) - Create clear changelogs from commits or summaries.
- [content-research-writer/](./content-research-writer/) - Research and draft content with sourced citations.
- [diasporic-intelligence](https://github.com/MinistaJazz/diasporic-intelligence) - External repo: source-credit skill for consent-governed lineage AI with attribution, provenance, revocation, and non-impersonation boundaries.
- [novel-writing](https://github.com/wgwtest/novel-writing) - External repo: public Codex skill for fiction planning, chapter drafting, scene continuation, and revision.
- [tailored-resume-generator/](./tailored-resume-generator/) - Tailor resumes to job descriptions with quantified impact.
- [unslop](https://github.com/MohamedAbdallah-14/unslop) - External repo: CLI and MCP server that removes AI writing patterns from text: tricolons, em-dash overuse, hedging stacks, and sycophancy openers. Works with Codex, Claude Code, Gemini CLI, and Cursor. Five intensity levels and a lint-only audit mode.

### Data & Analysis

- [spreadsheet-formula-helper/](./spreadsheet-formula-helper/) - Write and debug spreadsheet formulas, pivots, and array formulas.
- [competitive-ads-extractor/](./competitive-ads-extractor/) - Analyze competitor ads and extract structured insights.
- [datadog-logs/](./datadog-logs/) - Filter Datadog logs from the shell via the Composio CLI, with JSON-friendly output and digest workflows.
- [developer-growth-analysis/](./developer-growth-analysis/) - Analyze Codex chat history for coding patterns and learning gaps.
- [lead-research-assistant/](./lead-research-assistant/) - Research leads and enrich records with firmographic data.
- [domain-name-brainstormer/](./domain-name-brainstormer/) - Brainstorm available domain names with criteria and checks.
- [raffle-winner-picker/](./raffle-winner-picker/) - Randomly select winners with audit-friendly logs.
- [langsmith-fetch/](./langsmith-fetch/) - Pull LangSmith project/test data for analysis.
- [helium-mcp/](./helium-mcp/) - Search real-time news with bias scoring, get live market data, ML options pricing, and balanced news synthesis via MCP.

### Meta & Utilities

- [brand-guidelines/](./brand-guidelines/) - Apply OpenAI/Codex brand colors and typography to artifacts.
- [agent-deep-links/](./agent-deep-links/) - Build and validate deep links for Codex, Cursor, and VS Code with Slack-safe formatting and fallback guidance.
- [canvas-design/](./canvas-design/) - Generate structured canvas layouts and design artifacts.
- [image-enhancer/](./image-enhancer/) - Upscale and refine images with configurable presets.
- [slack-gif-creator/](./slack-gif-creator/) - Generate GIFs for Slack with captions and styling.
- [theme-factory/](./theme-factory/) - Create reusable theme tokens and palettes.
- [video-downloader/](./video-downloader/) - Download and prepare videos for offline review.
- [template-skill/](./template-skill/) - Starter template for building new skills.
- [skill-installer/](./skill-installer/) - Helper scripts to install skills from curated lists or GitHub paths.
- [skill-creator/](./skill-creator/) - Guidance for building effective Codex skills with progressive disclosure.
- [Unified AI System](https://github.com/happy520ai/unified-ai-system) - Public Preview of a terminal-first, self-hosted AI gateway with a Codex plugin and bounded MCP tools for provider-free prompt enhancement, health, readiness, credential-free fake-provider chat, knowledge, workflows, workforce inspection and read-only agent governance.

## Using Skills in Codex

- Skills live in `$CODEX_HOME/skills` (default `~/.codex/skills`). Each subfolder needs a `SKILL.md` with `name` and `description` frontmatter.
- After installing or updating a skill, restart Codex so it reloads metadata.
- In a session, describe the task naturally; Codex auto-triggers skills whose descriptions match the request. You can also mention a skill by name if you want it considered.
- To verify installation, list installed skills (`ls ~/.codex/skills`) and inspect metadata (`head ~/.codex/skills/<skill>/SKILL.md`).

## Creating Skills

Skill layout:

```
skill-name/
├── SKILL.md          # Required: instructions + YAML frontmatter
├── scripts/          # Optional: helper scripts for deterministic steps
├── references/       # Optional: long-form docs loaded only when needed
└── assets/           # Optional: templates or files used in outputs
```

Basic SKILL.md template:

```markdown
---
name: my-skill-name
description: What the skill does and when Codex should use it.
---

# My Skill Name

Clear instructions and steps for Codex to execute the task.
```

Best practices:

- Keep the `description` exhaustive about when to trigger; keep the body focused on execution steps.
- Use progressive disclosure: put detailed references in `references/` and call them out from `SKILL.md` only when needed.
- Include scripts for repeatable or deterministic operations; mention when Codex should run them.
- Avoid extra docs (README, changelog) inside the skill folder to keep context lean.

## Contributing

PRs welcome. Add real, reusable skills, keep descriptions precise, and include any needed scripts or references. If you add new skills, ensure the `description` clearly states when Codex should trigger and test that metadata fits within context limits.

## Join the Community

- [Join our Discord](https://discord.com/invite/composio) - Chat with other developers building Codex skills.
- [Follow on X](https://twitter.com/composio) - Updates on new skills and features.
- Questions? [support@composio.dev](mailto:support@composio.dev)

## Resources

- [Top Codex Skills](https://composio.dev/content/top-codex-skills)

---

<p align="center">
  <b>Join thousands of developers building agents that ship</b>
</p>

<p align="center">
  <a href="https://dashboard.composio.dev/login?utm_source=Github&utm_content=AwesomeCodexSkills">
    <img src="https://img.shields.io/badge/Get_Started_Free-4F46E5?style=for-the-badge" alt="Get Started"/>
  </a>
</p>
