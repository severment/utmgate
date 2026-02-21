# Before / After Examples

Real examples of what changes when an agent has utmgate loaded. Each section shows what the agent does without the skill vs. with it.

---

## URL Hygiene

### Sharing a link in a tweet

```
BEFORE:
https://myapp.com/pricing

AFTER:
https://myapp.com/pricing?utm_source=twitter&utm_medium=social&utm_campaign=myapp-launch
```

### Link in a README

```
BEFORE:
Check out the [documentation](https://myapp.com/docs)

AFTER:
Check out the [documentation](https://myapp.com/docs?utm_source=github&utm_medium=referral&utm_campaign=myapp-readme)
```

### Link in a newsletter

```
BEFORE:
Read the full announcement: https://myapp.com/blog/v2

AFTER:
Read the full announcement: https://myapp.com/blog/v2?utm_source=newsletter&utm_medium=email&utm_campaign=myapp-v2-announcement
```

### Multiple links in the same post

```
BEFORE:
Try the [demo](https://myapp.com/demo) or read the [docs](https://myapp.com/docs)

AFTER:
Try the [demo](https://myapp.com/demo?utm_source=twitter&utm_medium=social&utm_campaign=myapp-launch&utm_content=demo-link)
or read the [docs](https://myapp.com/docs?utm_source=twitter&utm_medium=social&utm_campaign=myapp-launch&utm_content=docs-link)
```

---

## Tweets

### Generic launch

```
BEFORE:
Excited to announce MyApp v2.0! We've been working hard and are thrilled to share our new
AI-powered features. Check it out! 🚀🎉🔥 https://myapp.com

AFTER:
MyApp now auto-tags every URL your agent touches.
No more "direct / none" in your analytics.
https://myapp.com?utm_source=twitter&utm_medium=social&utm_campaign=myapp-v2-launch
```

### Feature announcement

```
BEFORE:
New feature alert! 🚨 We've added real-time collaboration to MyApp! Multiple users can now
edit simultaneously. We're so excited about this one! Try it now → https://myapp.com/collab

AFTER:
Tired of "I'll send you the latest version"?
MyApp now has real-time collab. Two people, one doc, no conflicts.
https://myapp.com/collab?utm_source=twitter&utm_medium=social&utm_campaign=myapp-realtime-collab
```

### Tool / utility launch

```
BEFORE:
Just released my new CLI tool for managing environment variables! It's super powerful
and easy to use. Check it out on GitHub! 💪🔧 https://github.com/user/envtool

AFTER:
If you've ever deployed with the wrong .env, this is for you.
envtool validates env vars before deploy. Catches mismatches, missing keys, wrong formats.
https://github.com/user/envtool?utm_source=twitter&utm_medium=social&utm_campaign=envtool-launch
```

---

## Show HN

### Title

```
BEFORE:
Show HN: MyApp - An AI-Powered Open-Source Marketing Automation Platform for Modern Developer Teams

AFTER:
Show HN: MyApp – one file that makes your coding agent add UTMs automatically
```

### First comment

```
BEFORE:
Hi HN! We're excited to share MyApp, a revolutionary platform that leverages cutting-edge
AI technology to seamlessly automate marketing workflows for developer teams. Our platform
integrates with 50+ tools and provides enterprise-grade analytics.

AFTER:
I kept shipping features with my coding agent and then checking analytics the next day.
Everything was "direct / none." Every link, every page, every social post — invisible.

I tried adding UTMs manually. Lasted about two days before I forgot again.

So I wrote a skill file — a single markdown file that goes in the agent's rules directory.
Now every time the agent ships something, it auto-adds UTMs, checks for OG tags, drafts
a tweet, and wires up attribution on signup forms.

It's one file, no dependencies, works with Claude Code and Cursor. Opinionated on purpose.

Limitations: only tested with Claude Code extensively. Cursor support is based on their
rules format but I haven't done deep testing. No support for paid ad UTMs yet.

Would love feedback on what behaviors are missing.
```

---

## README First Lines

### Developer tool

```
BEFORE:
MyApp is a powerful, AI-driven, open-source tool for marketing automation.

AFTER:
Your agent ships code but forgets to tell anyone. MyApp fixes that.
```

### CLI utility

```
BEFORE:
envtool is a lightweight, cross-platform environment variable management tool.

AFTER:
Stop deploying with the wrong .env. envtool catches mismatches before your code ships.
```

### Framework / library

```
BEFORE:
FastForm is an advanced, type-safe form validation library for React applications.

AFTER:
React forms that validate themselves. No boilerplate. No runtime errors. Just types.
```

### API wrapper

```
BEFORE:
stripe-helper is a comprehensive wrapper around the Stripe API providing simplified access
to payment processing functionality.

AFTER:
Stripe's API is powerful but verbose. stripe-helper cuts a checkout integration from 200 lines to 12.
```

---

## Meta Descriptions

```
BEFORE:
MyApp - the best open source marketing tool for developers and AI agents

AFTER:
Stop losing traffic to "direct / none". One skill file makes your agent marketing-aware.
```

```
BEFORE:
envtool - environment variable management tool for Node.js developers

AFTER:
Validate your .env files before deploy. Catches missing keys, type mismatches, and wrong formats in CI.
```

```
BEFORE:
FastForm - the most advanced React form library with TypeScript support

AFTER:
React forms that validate from your types. No schemas to write. No runtime surprises. Works with any UI kit.
```

---

## Landing Pages

### Headline

```
BEFORE:
Welcome to MyApp

AFTER:
Every link your agent ships is invisible to analytics. Fix it with one file.
```

```
BEFORE:
envtool - Environment Variable Manager

AFTER:
Catch the wrong .env before it hits production.
```

### Subhead

```
BEFORE:
A powerful platform for modern developers

AFTER:
One markdown file that makes Claude Code, Cursor, and any coding agent handle UTMs, OG tags,
attribution, and launch copy automatically.
```

### CTA

```
BEFORE:
Learn More    |    Get Started    |    View Docs    |    Contact Us

AFTER:
Copy the skill file into your project →
```

---

## LinkedIn Posts

```
BEFORE:
🚀 Thrilled to announce that after months of hard work, our team has released MyApp v2.0!

This revolutionary platform leverages AI to seamlessly automate your marketing workflow.
We've been heads down building and are so proud of what we've accomplished.

Features include:
✅ UTM tracking
✅ OG tag generation
✅ Analytics integration
✅ And much more!

Check it out: https://myapp.com

#AI #Marketing #DevTools #Launch #Startup #Innovation

AFTER:
Last month I shipped 4 features. None of them showed up in my analytics.

Every link was "direct / none." Every shared URL had no preview card. Every signup had
zero attribution data. I had no idea what was working.

The problem wasn't me — it was my agent. It does exactly what I tell it to. And I never
tell it to add UTMs, write OG tags, or wire up attribution.

So I wrote one file — a skill file that sits in the agent's rules directory. Now every
time it ships, it handles the marketing basics I'd otherwise skip:

→ UTMs on every URL
→ OG tags on every page
→ Attribution cookies that survive navigation
→ Draft tweets and Show HN posts from the project context

It's called utmgate and it's a single markdown file. No package. No API. No build step.

https://github.com/user/utmgate?utm_source=linkedin&utm_medium=social&utm_campaign=utmgate-launch
```

---

## OG Tags

### Page without OG tags

```html
BEFORE:
<head>
  <title>MyApp</title>
</head>

AFTER:
<head>
  <title>MyApp – make your coding agent marketing-aware</title>
  <meta name="description" content="Stop losing traffic to 'direct / none'. One skill file makes your agent handle UTMs, OG tags, and attribution automatically." />
  <meta property="og:title" content="Make your coding agent marketing-aware" />
  <meta property="og:description" content="One file. Drop it in. Your agent handles UTMs, OG tags, and attribution on every ship." />
  <meta property="og:image" content="https://myapp.com/og-image.png" />
  <meta property="og:url" content="https://myapp.com" />
  <meta name="twitter:card" content="summary_large_image" />
  <meta name="twitter:title" content="Make your coding agent marketing-aware" />
  <meta name="twitter:description" content="One file. Drop it in. Your agent handles UTMs, OG tags, and attribution on every ship." />
  <meta name="twitter:image" content="https://myapp.com/og-image.png" />
  <link rel="canonical" href="https://myapp.com" />
  <link rel="icon" href="/favicon.ico" />
</head>
```

---

## Attribution Plumbing

### Signup form without attribution

```html
BEFORE:
<form action="/signup" method="POST">
  <input type="email" name="email" placeholder="you@example.com" />
  <button type="submit">Sign up</button>
</form>

AFTER:
<form action="/signup" method="POST">
  <input type="email" name="email" placeholder="you@example.com" />
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
  <button type="submit">Sign up</button>

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
</form>
```

### Signup handler without attribution

```typescript
// BEFORE:
app.post('/signup', async (req, res) => {
  const user = await db.user.create({
    data: { email: req.body.email }
  });
  res.redirect('/welcome');
});

// AFTER:
app.post('/signup', async (req, res) => {
  const user = await db.user.create({
    data: { email: req.body.email }
  });
  await db.userAttribution.create({
    data: {
      userId: user.id,
      ftSource: req.body.ft_source || null,
      ftMedium: req.body.ft_medium || null,
      ftCampaign: req.body.ft_campaign || null,
      ftReferrer: req.body.ft_referrer || null,
      ltSource: req.body.lt_source || null,
      ltMedium: req.body.lt_medium || null,
      ltCampaign: req.body.lt_campaign || null,
      ltReferrer: req.body.lt_referrer || null,
      landingPage: req.body.landing_page || null,
      signupPage: req.body.signup_page || null,
    },
  });
  res.redirect('/welcome');
});
```
