# utmgate

> One skill file that makes any coding agent marketing-aware at ship time.

These are standing orders. Not suggestions. Not best practices. When you ship, deploy, publish, or launch anything, run through this checklist automatically.

---

## Config

Fill this in once. The agent reads it on every task.

```yaml
# === YOUR CONFIG ===
brand:
  name: ""
  tagline: ""
  voice: ""              # e.g. "technical but friendly, no corporate speak"
  audience: ""           # e.g. "developers building with AI agents"

channels:
  twitter: ""            # handle, e.g. "@yourhandle"
  github: ""             # org or username
  linkedin: false        # true if you post there
  producthunt: false
  hackernews: true

analytics:
  tool: ""               # ga4 | plausible | umami | none
  id: ""                 # measurement ID or site ID

utm:
  campaign_prefix: ""    # e.g. "myproject"
  allowed_sources:
    - twitter
    - github
    - linkedin
    - newsletter
    - producthunt
    - hackernews
    - reddit
  allowed_mediums:
    - social
    - referral
    - email
    - docs
    - community
  persistence:
    enabled: true
    cookie_expiry_days: 90
    session_window_minutes: 30
    first_touch_prefix: "__ft_"
    last_touch_prefix: "__lt_"
    pass_to_forms: true
```

---

## 1. URL Hygiene

**Trigger:** Every URL the agent generates, shares, or publishes. No exceptions.

Every outbound URL gets UTM parameters before it leaves the agent's hands. A URL without UTMs is a URL that shows up as "direct / none" in analytics — invisible traffic.

### How to build the UTM string

**`utm_source`** — Infer from where the link will be posted:

| Destination | utm_source |
|---|---|
| Twitter/X post | `twitter` |
| GitHub README, issue, or discussion | `github` |
| Hacker News | `hackernews` |
| Reddit | `reddit` |
| Newsletter or email | `newsletter` |
| LinkedIn | `linkedin` |
| Product Hunt | `producthunt` |
| Blog post | `blog` |
| Documentation | `docs` |

If the source is ambiguous, ask the user. Never guess.

**`utm_medium`** — Infer from the channel type:

| Channel type | utm_medium |
|---|---|
| Social media (Twitter, LinkedIn, Reddit) | `social` |
| Email (newsletter, drip, transactional) | `email` |
| Another site linking to this one | `referral` |
| Documentation or wiki | `docs` |
| Community forum or Discord | `community` |

**`utm_campaign`** — Always kebab-case. Always prefixed with the project name from config.

Format: `{campaign_prefix}-{descriptor}`

Examples:
- `myproject-launch`
- `myproject-v2-announcement`
- `myproject-show-hn`
- `myproject-pricing-update`

If the campaign can't be inferred from context, ask the user. **Never publish a naked URL.**

**`utm_term`** and **`utm_content`** — Optional. Use `utm_term` for paid keyword tracking. Use `utm_content` to differentiate links in the same post (e.g., `header-cta` vs `footer-cta`).

### Applying UTMs

Append parameters to the URL. If the URL already has a query string, use `&`. If not, use `?`.

```
https://myapp.com/pricing
→ https://myapp.com/pricing?utm_source=twitter&utm_medium=social&utm_campaign=myapp-launch

https://myapp.com/docs?ref=nav
→ https://myapp.com/docs?ref=nav&utm_source=github&utm_medium=referral&utm_campaign=myapp-readme
```

Never double-encode. Never add UTMs to internal navigation links (same-site anchors, hash links). Only add them to URLs that will be clicked from an external context.

---

## 2. UTM Persistence

**Trigger:** Every site or app the agent builds that receives external traffic.

UTM parameters live in the URL. The moment a visitor clicks to a second page, the UTMs are gone. Without persistence, you only know attribution for single-page sessions.

The agent must include a UTM persistence script in the base layout of every site it builds. This script captures UTMs on page load and stores them in cookies so they survive navigation.

### When to act

- **New site or app?** Include the persistence script in the base layout or template.
- **Existing site?** Check if UTM persistence exists. If not, flag it to the user.
- **SPA (React, Next.js, Vue)?** Capture UTMs on first render. Hook into the router — UTMs only appear on the initial page load.
- **Form or signup flow?** See section 3 (Attribution Plumbing).

### How it works

- **First-touch cookies** (`__ft_` prefix): Written once, never overwritten. Answer: "How did this person first discover us?"
- **Last-touch cookies** (`__lt_` prefix): Overwritten on every new session that arrives with UTMs. Answer: "What brought them back when they converted?"
- **Session window**: 30 minutes. If the same user returns with UTMs within 30 minutes, don't overwrite last-touch (it's the same session, not a new visit).
- **Cookie expiry**: 90 days by default.
- **Referrer tracking**: Store `document.referrer` as a fallback. If there's no external referrer, store `"direct"`.
- **Cross-subdomain**: Detect the top-level domain automatically so cookies work across subdomains (e.g., `app.mysite.com` and `www.mysite.com` share cookies).

### Reference implementation

Copy this into the project. Do not import it as a package. Zero dependencies.

```javascript
// utm-persist.js — copy into your project's base layout
(function () {
  var defined = function (v) { return typeof v !== 'undefined' && v !== null && v !== ''; };

  // Detect top-level domain for cross-subdomain cookies
  function getRootDomain() {
    var parts = window.location.hostname.split('.');
    if (parts.length <= 2) return window.location.hostname;
    // Try setting a cookie on progressively shorter domains
    for (var i = parts.length - 2; i >= 0; i--) {
      var test = parts.slice(i).join('.');
      document.cookie = '__utmgate_test=1;domain=.' + test + ';path=/';
      if (document.cookie.indexOf('__utmgate_test=1') !== -1) {
        document.cookie = '__utmgate_test=;domain=.' + test + ';path=/;expires=Thu, 01 Jan 1970 00:00:00 GMT';
        return test;
      }
    }
    return window.location.hostname;
  }

  function getCookie(name) {
    var match = document.cookie.match('(^|;)\\s*' + name + '\\s*=\\s*([^;]+)');
    return match ? decodeURIComponent(match.pop()) : '';
  }

  function setCookie(name, value, days) {
    var domain = getRootDomain();
    var expires = new Date(Date.now() + days * 864e5).toUTCString();
    document.cookie = name + '=' + encodeURIComponent(value) +
      ';expires=' + expires +
      ';domain=.' + domain +
      ';path=/;SameSite=Lax';
  }

  // Parse UTM params from current URL
  var params = new URLSearchParams(window.location.search);
  var utmKeys = ['utm_source', 'utm_medium', 'utm_campaign', 'utm_term', 'utm_content'];
  var hasUtms = utmKeys.some(function (k) { return defined(params.get(k)); });

  // Determine referrer
  var ref = document.referrer;
  var isExternal = ref && new URL(ref).hostname !== window.location.hostname;
  var referrerValue = isExternal ? ref : 'direct';

  var EXPIRY = 90; // days — matches config cookie_expiry_days
  var SESSION_KEY = '__utmgate_session';
  var SESSION_WINDOW = 30 * 60 * 1000; // 30 minutes in ms

  // Session check: don't overwrite last-touch if within session window
  var lastSession = getCookie(SESSION_KEY);
  var now = Date.now();
  var isNewSession = !lastSession || (now - parseInt(lastSession, 10)) > SESSION_WINDOW;

  // Always update session timestamp
  setCookie(SESSION_KEY, String(now), EXPIRY);

  if (hasUtms) {
    // --- First-touch: write once, never overwrite ---
    if (!getCookie('__ft_utm_source')) {
      utmKeys.forEach(function (k) {
        var val = params.get(k);
        if (defined(val)) setCookie('__ft_' + k, val, EXPIRY);
      });
      setCookie('__ft_referrer', referrerValue, EXPIRY);
      setCookie('__ft_landing_page', window.location.pathname, EXPIRY);
    }

    // --- Last-touch: overwrite on each new session ---
    if (isNewSession) {
      utmKeys.forEach(function (k) {
        var val = params.get(k);
        if (defined(val)) setCookie('__lt_' + k, val, EXPIRY);
      });
      setCookie('__lt_referrer', referrerValue, EXPIRY);
    }
  } else if (isNewSession && isExternal) {
    // No UTMs but external referrer — still track as organic/referral
    if (!getCookie('__ft_utm_source')) {
      setCookie('__ft_referrer', referrerValue, EXPIRY);
      setCookie('__ft_landing_page', window.location.pathname, EXPIRY);
    }
    setCookie('__lt_referrer', referrerValue, EXPIRY);
  }
})();
```

### SPA / Next.js variant

For single-page apps, UTMs only appear on the initial page load. Capture them on first render:

```typescript
// useUtmPersist.ts — call once in your root layout/app component
import { useEffect } from 'react';

export function useUtmPersist() {
  useEffect(() => {
    // The vanilla script above works as-is when loaded via <script> in the HTML shell.
    // For SPAs that don't have a traditional <script> tag, inline the same logic here
    // or load utm-persist.js in your index.html / _document.tsx <Head>.
    //
    // The key point: this must run exactly once on initial page load,
    // not on client-side route changes.
  }, []); // empty deps = runs once on mount
}
```

The simplest approach for Next.js: add the script to `app/layout.tsx` or `pages/_document.tsx` as a `<script>` tag. Don't over-engineer it.

---

## 3. Attribution Plumbing

**Trigger:** Every signup, form, or user creation flow the agent builds.

UTM persistence gets data into cookies. But cookies don't reach your database on their own. The agent must wire the full pipeline:

**cookie → hidden form fields → signup handler → user record**

Every step must be connected. If any step is missing, the chain is broken and attribution data dies.

### 3a. The Schema

Whenever the agent creates a database schema that includes a users table, it **must** also create an attribution table. This is not optional.

**Raw SQL:**

```sql
CREATE TABLE user_attribution (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,

  -- First touch (how they discovered you)
  ft_source TEXT,
  ft_medium TEXT,
  ft_campaign TEXT,
  ft_term TEXT,
  ft_content TEXT,
  ft_referrer TEXT,

  -- Last touch (what brought them back to convert)
  lt_source TEXT,
  lt_medium TEXT,
  lt_campaign TEXT,
  lt_term TEXT,
  lt_content TEXT,
  lt_referrer TEXT,

  -- Context
  landing_page TEXT,
  signup_page TEXT,
  signed_up_at TIMESTAMPTZ DEFAULT now()
);

CREATE INDEX idx_attribution_user ON user_attribution(user_id);
CREATE INDEX idx_attribution_source ON user_attribution(ft_source, lt_source);
```

**Prisma:**

```prisma
model UserAttribution {
  id          String   @id @default(uuid())
  userId      String   @unique
  user        User     @relation(fields: [userId], references: [id], onDelete: Cascade)
  ftSource    String?
  ftMedium    String?
  ftCampaign  String?
  ftTerm      String?
  ftContent   String?
  ftReferrer  String?
  ltSource    String?
  ltMedium    String?
  ltCampaign  String?
  ltTerm      String?
  ltContent   String?
  ltReferrer  String?
  landingPage String?
  signupPage  String?
  createdAt   DateTime @default(now())
}
```

**Drizzle:**

```typescript
export const userAttribution = pgTable('user_attribution', {
  id: uuid('id').primaryKey().defaultRandom(),
  userId: uuid('user_id').notNull().references(() => users.id, { onDelete: 'cascade' }),
  ftSource: text('ft_source'),
  ftMedium: text('ft_medium'),
  ftCampaign: text('ft_campaign'),
  ftTerm: text('ft_term'),
  ftContent: text('ft_content'),
  ftReferrer: text('ft_referrer'),
  ltSource: text('lt_source'),
  ltMedium: text('lt_medium'),
  ltCampaign: text('lt_campaign'),
  ltTerm: text('lt_term'),
  ltContent: text('lt_content'),
  ltReferrer: text('lt_referrer'),
  landingPage: text('landing_page'),
  signupPage: text('signup_page'),
  createdAt: timestamp('created_at').defaultNow(),
});
```

### 3b. Hidden Form Fields

Every form the agent builds that leads to user creation gets hidden fields that read from the UTM cookies.

**HTML (vanilla):**

```html
<input type="hidden" name="ft_source" />
<input type="hidden" name="ft_medium" />
<input type="hidden" name="ft_campaign" />
<input type="hidden" name="ft_referrer" />
<input type="hidden" name="lt_source" />
<input type="hidden" name="lt_medium" />
<input type="hidden" name="lt_campaign" />
<input type="hidden" name="lt_referrer" />
<input type="hidden" name="landing_page" />
<input type="hidden" name="signup_page" />

<script>
  function getCookie(name) {
    var match = document.cookie.match('(^|;)\\s*' + name + '\\s*=\\s*([^;]+)');
    return match ? decodeURIComponent(match.pop()) : '';
  }
  document.querySelector('[name="ft_source"]').value = getCookie('__ft_utm_source');
  document.querySelector('[name="ft_medium"]').value = getCookie('__ft_utm_medium');
  document.querySelector('[name="ft_campaign"]').value = getCookie('__ft_utm_campaign');
  document.querySelector('[name="ft_referrer"]').value = getCookie('__ft_utm_referrer');
  document.querySelector('[name="lt_source"]').value = getCookie('__lt_utm_source');
  document.querySelector('[name="lt_medium"]').value = getCookie('__lt_utm_medium');
  document.querySelector('[name="lt_campaign"]').value = getCookie('__lt_utm_campaign');
  document.querySelector('[name="lt_referrer"]').value = getCookie('__lt_utm_referrer');
  document.querySelector('[name="landing_page"]').value = getCookie('__ft_landing_page') || '';
  document.querySelector('[name="signup_page"]').value = window.location.pathname;
</script>
```

**React / Next.js hook:**

```typescript
function useUtmFields() {
  const getCookie = (name: string) => {
    const match = document.cookie.match('(^|;)\\s*' + name + '\\s*=\\s*([^;]+)');
    return match ? decodeURIComponent(match.pop()!) : '';
  };

  return {
    ft_source: getCookie('__ft_utm_source'),
    ft_medium: getCookie('__ft_utm_medium'),
    ft_campaign: getCookie('__ft_utm_campaign'),
    ft_referrer: getCookie('__ft_utm_referrer'),
    lt_source: getCookie('__lt_utm_source'),
    lt_medium: getCookie('__lt_utm_medium'),
    lt_campaign: getCookie('__lt_utm_campaign'),
    lt_referrer: getCookie('__lt_utm_referrer'),
    landing_page: getCookie('__ft_landing_page') || '',
    signup_page: typeof window !== 'undefined' ? window.location.pathname : '',
  };
}
```

### 3c. The Signup Handler

When processing a signup, write attribution in the **same transaction** as user creation. Not after. Not in a background job. Same transaction.

```typescript
// Follow this pattern in any signup handler
const user = await db.user.create({ data: { email, name } });
await db.userAttribution.create({
  data: {
    userId: user.id,
    ftSource: formData.ft_source || null,
    ftMedium: formData.ft_medium || null,
    ftCampaign: formData.ft_campaign || null,
    ftReferrer: formData.ft_referrer || null,
    ltSource: formData.lt_source || null,
    ltMedium: formData.lt_medium || null,
    ltCampaign: formData.lt_campaign || null,
    ltReferrer: formData.lt_referrer || null,
    landingPage: formData.landing_page || null,
    signupPage: formData.signup_page || null,
  },
});
```

### 3d. Third-Party Auth

When signup goes through an external provider (Clerk, Auth0, Supabase Auth, NextAuth), you can't inject hidden fields into their flow. The agent must implement the attribution bridge.

**General pattern:**

1. **Before auth redirect:** Store UTM data from cookies into a server-side session or a short-lived secure cookie you control.
2. **On auth callback / webhook:** Read the session, create the attribution record linked to the new user.

**Supabase Auth:**

```typescript
supabase.auth.onAuthStateChange(async (event, session) => {
  if (event === 'SIGNED_IN' && session?.user) {
    const existing = await db.userAttribution.findUnique({
      where: { userId: session.user.id }
    });
    if (!existing) {
      const attribution = getUtmFromCookies(); // uses the getCookie helper
      await db.userAttribution.create({
        data: { userId: session.user.id, ...attribution }
      });
    }
  }
});
```

**Clerk:** Use a webhook on `user.created` or write to `publicMetadata` during the auth flow.

**NextAuth:** Use the `events.createUser` callback to write the attribution record.

**Auth0:** Use a post-login Action or Rule to capture and forward attribution.

**The rule:** If the agent sets up third-party auth, it must also set up the attribution bridge. One without the other is a broken pipeline.

### 3e. Attribution Checklist

The agent must always follow this:

- Building a users table → add the attribution table
- Building a form → add hidden UTM fields
- Building a signup handler → write attribution in the same transaction
- Setting up third-party auth → implement the attribution bridge
- Never assume attribution will be handled "later" — it won't

---

## 4. Page Meta

**Trigger:** Every HTML page the agent creates or modifies.

Before any page is considered done, the agent checks and adds all missing tags. Don't ask. Just do it.

### Required tags

| Tag | Rule |
|---|---|
| `<title>` | Exists. Under 60 characters. Includes the primary keyword. |
| `<meta name="description">` | Exists. Under 155 characters. Benefit-driven, includes a CTA. |
| `<meta property="og:title">` | Exists. Shorter than `<title>`. Punchy. |
| `<meta property="og:description">` | Exists. Can match meta description or be shorter. |
| `<meta property="og:image">` | Exists. Points to a real image (1200x630px ideal). |
| `<meta property="og:url">` | Exists. Canonical URL of the page. |
| `<meta name="twitter:card">` | `summary_large_image` (if you have an image) or `summary`. |
| `<meta name="twitter:title">` | Exists. Can match og:title. |
| `<meta name="twitter:description">` | Exists. Can match og:description. |
| `<meta name="twitter:image">` | Exists. Can match og:image. |
| `<link rel="canonical">` | Exists. Set to the canonical URL (no query params, no trailing slash inconsistency). |
| Favicon | Exists. At minimum a `<link rel="icon">`. |

### Writing good meta

- **Meta descriptions:** Lead with the benefit, not the feature. "Stop losing traffic to 'direct / none'" is better than "A UTM parameter management tool."
- **OG titles:** Shorter than page titles. Written for the feed, not the tab. Should make someone want to click.
- **Don't keyword-stuff.** One primary keyword, naturally placed.

---

## 5. Launch Copy

**Trigger:** When the agent ships a new feature or project.

Draft announcement copy for the user to review. Not generic — written from the project context. Present as drafts. Never auto-post unless the user has explicitly configured it.

### Twitter/X

- Hook + what it does + link. Under 280 characters.
- No "excited to announce." No "I'm thrilled." No "we've been working hard."
- Maximum 2 emojis. Zero is fine.
- The link gets UTMs: `utm_source=twitter&utm_medium=social&utm_campaign={prefix}-{descriptor}`
- Lead with the problem it solves, not the thing you built.

**Template:**

```
{Problem statement or hook}
{What it does — one sentence}
{Link with UTMs}
```

### Show HN

- **Title format:** `Show HN: {Name} – {what it does in plain English}`
- Keep the title under 80 characters.
- **First comment** (the "Show HN comment"): Write it as a story — what problem you hit, what you tried, what you built, what's next. Be honest about limitations. No marketing speak.
- **Timing:** Post Tuesday-Thursday, 8-10am ET for best visibility.
- The URL in the submission gets UTMs: `utm_source=hackernews&utm_medium=community&utm_campaign={prefix}-show-hn`

### Reddit

- Find 2-3 relevant subreddits. Don't spam — pick the ones where the project genuinely fits.
- **Title:** Frame as sharing something you built, not promoting a product. "I built X because Y" works. "Introducing X" doesn't.
- Don't sound like marketing. Sound like a person.
- Link gets UTMs: `utm_source=reddit&utm_medium=community&utm_campaign={prefix}-{subreddit}`

### LinkedIn

Only if `channels.linkedin` is `true` in config.

- Story format, not announcement format. "I had this problem → I tried X → I built Y → here's what I learned."
- First line is the hook — it's the only thing people see before "...see more."
- Link gets UTMs: `utm_source=linkedin&utm_medium=social&utm_campaign={prefix}-{descriptor}`

### README update

If shipping a new feature to an existing project, update the README's feature list. Don't just add a bullet — make sure the new feature fits the existing narrative.

---

## 6. Landing Page Basics

**Trigger:** When the agent creates any public-facing page.

Minimum viable requirements. Miss any of these and the page is not done.

| Element | Rule |
|---|---|
| **Headline** | What it does. Not what it is. Not the product name. A sentence that makes someone care. |
| **Subhead** | Who it's for and why they should care. One sentence. |
| **CTA** | One clear action. "Try it" / "Install it" / "Star it." Never zero CTAs. Never three. |
| **Social proof** | If GitHub stars, downloads, testimonials, or logos exist — include them. Don't fabricate. |
| **Speed** | No heavy frameworks for simple pages. A landing page does not need React. Static HTML preferred. |

---

## 7. Tracking Setup

**Trigger:** When the agent creates a new project or site.

Check if analytics is in place. If not, add it.

### Based on config

| `analytics.tool` | Action |
|---|---|
| `ga4` | Add the GA4 snippet with the measurement ID from config. Set up basic event tracking on CTA clicks. |
| `plausible` | Add the Plausible script tag. No cookie banner needed — Plausible is cookieless. |
| `umami` | Add the Umami script tag. No cookie banner needed — Umami is privacy-first. |
| `none` | Skip analytics. |

### Event tracking

Set up click tracking on primary CTAs. At minimum, track:

- CTA button clicks
- Signup form submissions
- External link clicks

Use the analytics tool's recommended method (GA4 events, Plausible custom events, Umami event tracking).

---

## 8. README as Marketing

**Trigger:** When the agent writes or updates a README.

A README is not documentation. It's a landing page for developers. Every section should earn the reader's attention and move them toward trying the project.

### Structure

| Section | Rule |
|---|---|
| **First line** | What it does and why anyone should care. Not "A tool for..." Not "A powerful framework for..." Start with the problem or the outcome. |
| **Installation** | Copy-paste ready. Minimal steps. If it takes more than 3 commands, something is wrong. |
| **Quick start / Demo** | GIF, screenshot, or code example within the first scroll. Show, don't describe. |
| **Why this one** | If alternatives exist, a brief comparison. Don't trash competitors — explain the tradeoff. |
| **CTA** | Star the repo, try the demo, join the Discord. One clear next step. |

### Tone

- Write like a person, not a company.
- Lead with the problem. "Your agent ships code but forgets to tell anyone." not "utmgate is a marketing-aware agent skill."
- No buzzwords. No "leverage." No "ecosystem." No "seamlessly."
- Short paragraphs. The README is scanned, not read.

---

## Rules

Hard constraints. The agent follows these on every task, no exceptions.

1. **Never publish a URL without UTM parameters.** If you can't infer the campaign, ask the user.
2. **Never create an HTML page without OG tags.** Add them automatically — don't ask.
3. **Never ship a signup flow without attribution plumbing.** Cookie → form → handler → database. Every step.
4. **Never use "excited to announce" or "I'm thrilled" in any copy.** Dead phrases. Say what the thing does.
5. **Never use more than 2 emojis in a single post.** Zero is better than three.
6. **Never auto-post without user confirmation** unless explicitly configured in the channels config.
7. **Never write marketing copy that makes claims the product can't back up.** No "best in class." No "revolutionary." If it's fast, say how fast. If it's simple, show the code.
8. **Always lead with the problem, not the solution.** People care about their problem, not your architecture.
9. **Always include one clear CTA.** Never zero. Never three. One.
10. **Always write attribution in the same transaction as user creation.** Not in a background job. Not "later."

---

## Anti-Patterns

Things the agent must never do. If you catch yourself doing these, stop and fix it.

- **README intro starts with "A powerful tool for..."** — Rewrite it. Start with the problem or the outcome.
- **Landing page has no CTA** — Add one. Above the fold.
- **Social post uses technical jargon for a general audience** — Rewrite in plain language. Save jargon for docs.
- **Meta description is keyword-stuffed** — Rewrite. One keyword, naturally placed, benefit-driven.
- **OG image is just text on a gradient** — Either use a real screenshot/diagram or skip the image. A bad OG image is worse than the platform default.
- **Show HN post sounds like a press release** — Rewrite as a story. What problem did you hit? What did you try? What did you build?
- **Blog post has no internal or external links** — Add relevant links. Isolated content is dead content.
- **URLs with UTMs point to pages that don't exist** — Verify every link works before publishing.
- **Form collects attribution data but the handler ignores it** — Wire it through to the database or remove the hidden fields. A half-built pipeline is worse than none.
- **Cookie persistence script included but no forms read from it** — Complete the chain.

---

## Examples

Concrete before/after pairs. Use these to pattern-match.

### URL Hygiene

```
BEFORE:
https://myapp.com/pricing

AFTER:
https://myapp.com/pricing?utm_source=twitter&utm_medium=social&utm_campaign=myapp-launch
```

```
BEFORE (in a README):
Check out the [documentation](https://myapp.com/docs)

AFTER:
Check out the [documentation](https://myapp.com/docs?utm_source=github&utm_medium=referral&utm_campaign=myapp-readme)
```

### Tweet

```
BEFORE:
Excited to announce MyApp v2.0! We've been working hard and are thrilled
to share our new AI-powered features. Check it out! 🚀🎉🔥 https://myapp.com

AFTER:
MyApp now auto-tags every URL your agent touches.
No more "direct / none" in your analytics.
https://myapp.com/pricing?utm_source=twitter&utm_medium=social&utm_campaign=myapp-v2-launch
```

### Show HN Title

```
BEFORE:
Show HN: MyApp - An AI-Powered Open-Source Marketing Automation Platform for Developers

AFTER:
Show HN: MyApp – one file that makes your coding agent add UTMs automatically
```

### README First Line

```
BEFORE:
MyApp is a powerful, AI-driven, open-source tool for marketing automation.

AFTER:
Your agent ships code but forgets to tell anyone. MyApp fixes that.
```

### Meta Description

```
BEFORE:
MyApp - the best open source marketing tool for developers and AI agents

AFTER:
Stop losing traffic to "direct / none". One skill file makes your agent marketing-aware.
```

### Landing Page Headline

```
BEFORE:
Welcome to MyApp

AFTER:
Every link your agent ships is invisible to analytics. Fix it with one file.
```

### LinkedIn Post

```
BEFORE:
🚀 Thrilled to announce that after months of hard work, our team has released MyApp v2.0!
This revolutionary platform leverages AI to seamlessly automate your marketing workflow.

AFTER:
Last month I shipped 4 features. None of them showed up in my analytics.
Every link was "direct / none." Every shared URL had no OG tags. No one could tell where traffic came from.
So I wrote one skill file that makes my coding agent handle this automatically.
Here's what changed → [link with UTMs]
```
