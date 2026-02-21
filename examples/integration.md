# Integration Guide

How to load utmflow into your coding agent.

---

## Claude Code

Claude Code reads skill files from `.claude/skills/` in your project directory.

### Setup

```bash
mkdir -p .claude/skills
cp SKILL.md .claude/skills/utmflow.md
```

### Verify

Start a Claude Code session in your project and ask it to do something that triggers the skill — ship a feature, create a page, draft a tweet. The agent should automatically apply UTMs, add OG tags, and follow the skill's rules.

### Alternative: project-level CLAUDE.md

You can also reference the skill from your project's `CLAUDE.md`:

```markdown
# CLAUDE.md

## Skills
- Read and follow `.claude/skills/utmflow.md` on every task that involves shipping, deploying, publishing, or creating public-facing pages.
```

---

## Cursor

Cursor reads rules from `.cursor/rules/` in your project directory.

### Setup

```bash
mkdir -p .cursor/rules
cp SKILL.md .cursor/rules/utmflow.md
```

Cursor will automatically load the file as context when working in your project.

### Note on file size

If the skill file is too large for Cursor's context window, you can split the config block into a separate `.cursor/rules/utmflow-config.md` and reference it. But try the single file first — it's designed to fit.

---

## Windsurf

Windsurf reads rules from `.windsurfrules` or `.windsurf/rules/` in your project directory.

### Setup

```bash
mkdir -p .windsurf/rules
cp SKILL.md .windsurf/rules/utmflow.md
```

---

## Other Agents

Any agent that supports project-level instruction files can use utmflow. The general pattern:

1. Find where your agent reads project rules (usually a dotfile directory or a config file).
2. Copy `SKILL.md` into that location.
3. If the agent doesn't auto-load files from that directory, add a reference in the agent's main config file pointing to the skill.

### As a system prompt addition

If your agent uses a system prompt or custom instructions field, you can paste the contents of `SKILL.md` directly into it. This works with any agent, including API-based setups.

---

## Filling In The Config

After copying the file, fill in the config block at the top. This is the only setup step.

```yaml
brand:
  name: "your project name"
  tagline: "what it does in one line"
  voice: "your tone"           # e.g. "technical but friendly, no corporate speak"
  audience: "who uses it"      # e.g. "developers building with AI agents"

channels:
  twitter: "@yourhandle"
  github: "your-username"
  linkedin: false              # set true if you post there
  producthunt: false
  hackernews: true

analytics:
  tool: "plausible"            # ga4 | plausible | umami | none
  id: "your-site-id"

utm:
  campaign_prefix: "yourproject"
```

Everything else in the skill file works with these defaults. You don't need to touch the rest unless you want to customize behavior.

---

## Verifying It Works

After setup, test with these tasks:

1. **"Draft a tweet announcing this project."** — The agent should produce a tweet with UTMs on the link, no "excited to announce," max 2 emojis.

2. **"Create a landing page for this project."** — The agent should include a headline, subhead, one CTA, OG tags, meta description, and the UTM persistence script.

3. **"Set up a signup form."** — The agent should add hidden attribution fields and wire them through to the signup handler.

4. **"Write a README."** — The agent should lead with the problem, include copy-paste install instructions, and end with a CTA.

If any of these are missing, the skill isn't loaded correctly. Check that the file is in the right directory and that your agent reads from it.

---

## Updating

When a new version of utmflow is released:

```bash
# Re-download or copy the updated SKILL.md
cp /path/to/new/SKILL.md .claude/skills/utmflow.md
```

Your config block at the top won't be overwritten if you keep a backup. Alternatively, keep your config in a separate file and merge after updates.
