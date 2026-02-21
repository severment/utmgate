# utmgate 📡 — Making your agent marketing-aware at ship time

<!-- badges -->
[![MIT License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude_Code-compatible-6B4FBB.svg)](#claude-code)
[![Cursor](https://img.shields.io/badge/Cursor-compatible-00D1B2.svg)](#cursor)
[![Zero Dependencies](https://img.shields.io/badge/dependencies-0-brightgreen.svg)](#)

Your agent ships code but forgets to tell anyone. Every URL is "direct / none." Every page is missing OG tags. Every signup has zero attribution. utmgate is one markdown file that fixes all of it — drop it into your agent's skill directory and every ship, deploy, and publish gets the marketing basics handled automatically. Works with Claude Code, Cursor, Windsurf, and any agent that reads project-level instruction files.

## Quick start

Copy the skill file into your project. Fill in the config. Done.

```bash
# Claude Code
mkdir -p .claude/skills
cp SKILL.md .claude/skills/utmgate.md

# Cursor
mkdir -p .cursor/rules
cp SKILL.md .cursor/rules/utmgate.md

# Windsurf
mkdir -p .windsurf/rules
cp SKILL.md .windsurf/rules/utmgate.md
```

Fill in the config block at the top of the copied file:

```yaml
brand:
  name: "your project"
  tagline: "what it does in one line"
  voice: "technical but friendly"
  audience: "developers building with AI agents"

channels:
  twitter: "@yourhandle"
  github: "your-username"
  hackernews: true

analytics:
  tool: "plausible"          # ga4 | plausible | umami | none
  id: "your-site-id"

utm:
  campaign_prefix: "yourproject"
```

Now ask your agent to do anything that ships:

```bash
# The agent adds UTMs to every link, OG tags to every page, attribution to every form
"Create a landing page for this project"

# Launch copy follows tone rules — no 'excited to announce', problem-first, one CTA
"Draft a tweet and Show HN post for this launch"

# Signup forms get hidden attribution fields wired to the database automatically
"Build a waitlist signup with email and Supabase auth"

# README follows the skill's own rules — problem-first opener, copy-paste install, CTA
"Write the README"
```

## What it does

When your agent ships, deploys, or publishes anything, utmgate triggers 8 automatic behaviors. The agent treats these as standing orders, not suggestions.

| # | Behavior | Trigger | What happens |
|---|---|---|---|
| 1 | **URL Hygiene** | Every outbound URL | UTM parameters added. Source, medium, campaign inferred from context. Never publishes naked. |
| 2 | **UTM Persistence** | Every site/app that receives traffic | First-touch + last-touch cookies survive multi-page navigation. 30-min session window, 90-day expiry. |
| 3 | **Attribution Plumbing** | Every signup, form, or user creation | Full pipeline: cookie → hidden fields → handler → `user_attribution` table. SQL, Prisma, Drizzle schemas included. |
| 4 | **Page Meta** | Every HTML page created or modified | `<title>`, meta description, OG tags, Twitter cards, canonical URL, favicon — all added automatically. |
| 5 | **Launch Copy** | New feature or project shipped | Drafts for Twitter, Show HN, Reddit, LinkedIn. Tone rules enforced. Presented for review, never auto-posted. |
| 6 | **Landing Page Basics** | Any public-facing page | Headline (what it does), subhead (who it's for), one CTA, social proof. Static HTML preferred. |
| 7 | **Tracking Setup** | New project or site | Analytics snippet added (GA4/Plausible/Umami per config) with CTA event tracking. |
| 8 | **README as Marketing** | README written or updated | Problem-first opener, copy-paste install, demo/screenshot, comparison, CTA. |

## Integration

### Claude Code

```bash
mkdir -p .claude/skills
cp SKILL.md .claude/skills/utmgate.md
```

Or reference it from your project's `CLAUDE.md`:

```markdown
## Skills
- Read and follow `.claude/skills/utmgate.md` on every task that involves
  shipping, deploying, publishing, or creating public-facing pages.
```

### Cursor

```bash
mkdir -p .cursor/rules
cp SKILL.md .cursor/rules/utmgate.md
```

Cursor auto-loads rules from `.cursor/rules/`. No extra config needed.

### Windsurf

```bash
mkdir -p .windsurf/rules
cp SKILL.md .windsurf/rules/utmgate.md
```

### Any other agent

Copy `SKILL.md` into wherever your agent reads project-level instructions. If the agent uses a system prompt or custom instructions field, paste the contents of `SKILL.md` directly.

See [examples/integration.md](examples/integration.md) for detailed setup guides and verification steps.

## Highlights

- **One file, zero dependencies.** No packages, no build step, no API. Copy the file and go.
- **UTM persistence script included.** Vanilla JS, cross-subdomain, first-touch + last-touch cookies. Copy-paste into any project.
- **Full attribution pipeline.** Cookie → form → handler → database. Schemas for raw SQL, Prisma, and Drizzle. Third-party auth bridges for Supabase, Clerk, NextAuth, Auth0.
- **Launch copy with tone rules.** No "excited to announce." No emoji spam. Problem-first, under 280 chars, UTMs on every link.
- **10 hard rules the agent can't break.** Never publish naked URLs. Never ship pages without OG tags. Never skip attribution. Always one CTA.
- **Anti-pattern detection.** Catches README intros that start with "A powerful tool for...", landing pages with no CTA, Show HN posts that sound like press releases.
- **Before/after examples.** Concrete pairs for every behavior so the agent can pattern-match.

## What's in the skill file

```
SKILL.md
├── Config Block .............. YAML — your brand, channels, analytics, UTM settings
├── 1. URL Hygiene ............ Source/medium/campaign inference tables, formatting rules
├── 2. UTM Persistence ........ Complete vanilla JS script (~60 lines, zero deps)
├── 3. Attribution Plumbing
│   ├── 3a. Schema ............ SQL, Prisma, Drizzle for user_attribution table
│   ├── 3b. Hidden Fields ..... HTML snippet + React hook
│   ├── 3c. Signup Handler .... Same-transaction pattern
│   ├── 3d. Third-Party Auth .. Supabase, Clerk, NextAuth, Auth0 bridges
│   └── 3e. Checklist ......... What to always do
├── 4. Page Meta .............. Required tags table, writing guidance
├── 5. Launch Copy ............ Templates for Twitter, Show HN, Reddit, LinkedIn
├── 6. Landing Page Basics .... Headline/subhead/CTA/social proof/speed
├── 7. Tracking Setup ......... GA4, Plausible, Umami per config
├── 8. README as Marketing .... Structure, tone, anti-buzzword rules
├── Rules ..................... 10 hard constraints
├── Anti-Patterns ............. 10 mistakes to catch
└── Examples .................. 7 before/after pairs
```

## Config reference

| Key | Type | Purpose |
|---|---|---|
| `brand.name` | string | Project name — used in campaign prefixes and copy |
| `brand.tagline` | string | One-line description — used in meta tags and social posts |
| `brand.voice` | string | Tone guide — the agent matches this voice in all copy |
| `brand.audience` | string | Who it's for — shapes how the agent writes descriptions |
| `channels.twitter` | string | Your handle — used for Twitter card meta and tweet drafts |
| `channels.github` | string | Org or username — used for README CTAs and social links |
| `channels.linkedin` | boolean | If true, agent drafts LinkedIn posts |
| `channels.producthunt` | boolean | If true, agent drafts PH launch copy |
| `channels.hackernews` | boolean | If true, agent drafts Show HN posts |
| `analytics.tool` | `ga4` \| `plausible` \| `umami` \| `none` | Which analytics to set up |
| `analytics.id` | string | Measurement ID or site ID |
| `utm.campaign_prefix` | string | Prefix for all campaign names (kebab-case) |
| `utm.persistence.enabled` | boolean | Include UTM persistence script on sites |
| `utm.persistence.cookie_expiry_days` | number | Cookie lifetime (default 90) |
| `utm.persistence.session_window_minutes` | number | Session window before last-touch overwrites (default 30) |
| `utm.persistence.pass_to_forms` | boolean | Auto-inject hidden UTM fields in forms |

## Examples

```
URL HYGIENE
─────────────────────────────────────────────────────
Before: https://myapp.com/pricing
After:  https://myapp.com/pricing?utm_source=twitter&utm_medium=social&utm_campaign=myapp-launch

TWEET
─────────────────────────────────────────────────────
Before: Excited to announce MyApp v2.0! We've been working hard and are thrilled
        to share our new AI-powered features. Check it out! 🚀🎉🔥 https://myapp.com

After:  MyApp now auto-tags every URL your agent touches.
        No more "direct / none" in your analytics.
        https://myapp.com?utm_source=twitter&utm_medium=social&utm_campaign=myapp-v2-launch

SHOW HN TITLE
─────────────────────────────────────────────────────
Before: Show HN: MyApp - An AI-Powered Open-Source Marketing Automation Platform
After:  Show HN: MyApp – one file that makes your coding agent add UTMs automatically

README FIRST LINE
─────────────────────────────────────────────────────
Before: MyApp is a powerful, AI-driven, open-source tool for marketing automation.
After:  Your agent ships code but forgets to tell anyone. MyApp fixes that.

META DESCRIPTION
─────────────────────────────────────────────────────
Before: MyApp - the best open source marketing tool for developers and AI agents
After:  Stop losing traffic to "direct / none". One skill file makes your agent marketing-aware.
```

See [examples/before-after.md](examples/before-after.md) for 15+ expanded examples across all behaviors.

## More docs

| Doc | What's in it |
|---|---|
| [SKILL.md](SKILL.md) | **The product.** The actual skill file you copy into your project. |
| [examples/integration.md](examples/integration.md) | Setup guides for Claude Code, Cursor, Windsurf, and other agents. Verification steps. |
| [examples/before-after.md](examples/before-after.md) | Expanded before/after examples for URLs, tweets, Show HN, READMEs, LinkedIn, OG tags, forms, handlers. |
| [CONTRIBUTING.md](CONTRIBUTING.md) | How to contribute examples, edge cases, and new ORM/framework schemas. |

## Contributing

The best contributions are before/after examples from real projects, edge cases the skill doesn't handle, and new ORM/framework schemas. See [CONTRIBUTING.md](CONTRIBUTING.md).

## License

[MIT](LICENSE)
