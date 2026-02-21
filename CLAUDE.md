# 🤖 CLAUDE.md — utmflow

## What This Project Is

utmflow is a single markdown skill file (`SKILL.md`) that makes any coding agent marketing-aware at ship time. Not a tool, not an API, not a package — one opinionated file.

## The Problem It Solves

Agentic builders ship fast but marketing-blind. Every feature, page, and link goes out without:
- UTM parameters (traffic shows as "direct / none")
- OG tags (links look broken on social)
- Announcement copy (builder context-switches into marketing mode)
- CTAs (visitors land, look around, leave)
- Tracking (no idea if anyone cares)

The agent doesn't do these things because nobody told it to. This skill file tells it to.

## Target Audience

Agentic builders (indie hackers, solopreneurs, dev tool founders) who ship with Claude Code, Cursor, or similar and want their agent to handle the marketing they'd otherwise skip.

## What The Skill File Does (8 Behaviors)

1. **URL Hygiene** — Every URL gets UTM params. Source/medium/campaign inferred from context. Never publish a naked URL.
2. **UTM Persistence** — Cookie-based first-touch (`__ft_`) and last-touch (`__lt_`) attribution. 30-min session window, 90-day expiry. Survives multi-page navigation.
3. **Attribution Plumbing** — Full pipeline: cookie → hidden form fields → signup handler → `user_attribution` DB table. Includes schemas for raw SQL, Prisma, and Drizzle. Covers third-party auth bridges (Clerk, Auth0, Supabase Auth, NextAuth).
4. **Page Meta** — Auto-add `<title>`, meta description, OG tags, Twitter cards, canonical URL, favicon to every HTML page.
5. **Launch Copy** — Draft announcements for Twitter, Show HN, Reddit, LinkedIn, README. Tone rules enforced (no "excited to announce", max 2 emojis).
6. **Landing Page Basics** — Headline (what it does), subhead (who it's for), one CTA, social proof, static HTML preferred.
7. **Tracking Setup** — Add analytics snippet (GA4/Plausible/Umami per user config) and event tracking on CTAs.
8. **README as Marketing** — README is a developer landing page: problem-first opener, copy-paste install, GIF/screenshot, comparison, CTA.

## Repo Structure

```
utmflow/
├── CLAUDE.md           ← you are here
├── README.md           ← dogfooded with the skill's own advice
├── SKILL.md            ← the product (the actual skill file)
├── CONTRIBUTING.md
├── LICENSE             ← MIT
└── examples/
    ├── before-after.md ← expanded before/after examples
    └── integration.md  ← how to load into Claude Code, Cursor, etc.
```

No `src/`. No `package.json`. No build step. The skill file IS the product.

## SKILL.md Architecture

The skill file has these sections:
- **Config Block** — YAML at the top for user defaults (brand, channels, analytics, UTM settings, persistence config)
- **Core Behaviors** — The 8 standing orders listed above
- **Rules** — Hard constraints (never publish naked URLs, never create pages without OG tags, always one CTA, etc.)
- **Anti-patterns** — Mistakes to prevent (READMEs starting with "A powerful tool for...", landing pages with no CTA, Show HN posts that sound like press releases, etc.)
- **Examples** — Concrete before/after pairs for each behavior

## Key Design Decisions

- The persistence script is a ready-to-paste snippet, not an npm package. Zero dependencies.
- First-touch cookies are write-once (discovery). Last-touch cookies overwrite each session (activation).
- Attribution is written in the same DB transaction as user creation. Not after. Not in a background job.
- The agent treats all behaviors as standing orders, not suggestions.
- Copy tone: lead with the problem, not the solution. Benefit-driven, not feature-driven.

## Commit Rules

- **Never** append `Co-Authored-By` lines to commit messages.
- Write concise, imperative commit messages.

## Build Phases

1. Write & iterate `SKILL.md` (test by actually using it with an agent)
2. Dogfood the launch (use the skill to launch itself)
3. Ship to HN, Twitter, communities — collect feedback
4. Community contributions and iteration
