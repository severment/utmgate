# Contributing to utmgate

utmgate is a single skill file. Contributions should make the file more useful, more accurate, or easier to adopt. Not bigger for the sake of bigger.

## What to contribute

**High value:**

- Before/after examples from real projects — you used the skill and here's what changed
- Edge cases the skill doesn't handle — a framework, auth provider, or platform that breaks the current patterns
- Bug fixes in the reference implementations (persistence script, hidden fields, schemas)
- Improved inference rules for utm_source and utm_medium
- Better launch copy templates based on what actually performs

**Welcome:**

- Typo and clarity fixes
- New ORM/framework schema examples (e.g., SQLAlchemy, Django, Mongoose)
- Integration guides for agents not yet covered
- Anti-pattern additions from real mistakes

**Please don't:**

- Add dependencies. The skill file is zero-dependency by design.
- Propose splitting SKILL.md into multiple files. Resist this as long as possible.
- Add features that only apply to one specific SaaS (e.g., HubSpot-specific CRM fields). Keep it generic.
- Rewrite sections for style preference. The tone is intentional.

## How to contribute

1. Fork the repo
2. Create a branch (`git checkout -b add-django-schema`)
3. Make your changes
4. Test your changes by actually loading the skill into an agent and shipping something
5. Open a PR with:
   - What you changed and why
   - How you tested it (which agent, what you shipped, what the agent did differently)

## PR format

```
## What

One sentence describing the change.

## Why

The problem this fixes or the gap this fills.

## Tested with

- Agent: Claude Code / Cursor / other
- Task: what you asked the agent to do
- Result: what the agent did with the skill loaded
```

## File structure

```
utmgate/
├── README.md
├── SKILL.md            ← the product — most changes go here
├── CONTRIBUTING.md     ← you are here
├── LICENSE
└── examples/
    ├── before-after.md ← expanded examples
    └── integration.md  ← agent setup guides
```

If your contribution is a new example, add it to `examples/before-after.md`. If it's a new integration guide, add it to `examples/integration.md`. Core behavior changes go in `SKILL.md`.

## Code of conduct

Be useful. Be honest. Don't waste people's time.
