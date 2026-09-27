---
name: pbn-detector
description: >
  Evaluate websites for Private Blog Network (PBN) risk before acquiring backlinks.
  Use this skill whenever the user wants to check if websites are PBNs, evaluate
  backlink sources, audit link prospects, vet guest post sites, or assess whether
  sites selling backlinks are legitimate. Also use when the user mentions phrases
  like "is this site legit", "check these domains", "evaluate these backlinks",
  "PBN detection", "link audit", "backlink quality check", "guest post site review",
  or provides a list of URLs to vet for SEO safety. This skill handles bulk evaluation
  of multiple sites in parallel for efficiency.
---

# PBN Detector — Backlink Source Evaluation

You help SEO professionals and site owners evaluate whether prospective backlink
sources are Private Blog Networks (PBNs) or legitimate sites. PBNs are networks of
low-quality blogs created to manipulate Google rankings through artificial link
building. Getting backlinks from PBNs risks Google penalties that can devastate a
site's organic traffic.

## When to use this skill

Any time a user provides one or more URLs and wants to know whether they're safe
to get backlinks from — or when they ask you to check sites for PBN indicators.
The user might not use the term "PBN" explicitly; they might say things like
"are these sites legit?", "should I get links from these?", "check these domains
for me", or "evaluate these backlink prospects."

## How the evaluation works

### Step 1: Batch the sites

Group the URLs into batches of 4-5 sites each. This is the unit of work for
parallel evaluation — each batch gets its own subagent.

### Step 2: Dispatch parallel subagents

For each batch, spawn a subagent with the following investigation brief. Use the
Agent tool with subagent_type "general-purpose". Launch all batches simultaneously
in the same turn to maximize speed.

Each subagent should:

1. **Fetch the site** using `mcp__workspace__web_fetch`:
   - The homepage
   - The specific URL the user provided (if different from homepage)
   - /about or /about-us page
   - /contact page
   - /privacy-policy page
   - /write-for-us or /guest-post page (if it exists, that's a red flag)

2. **Search for external evidence** using `WebSearch`:
   - `"sitename.com" guest post` — checks if the site sells guest posts
   - `"sitename.com" vefogix OR guestpostsale OR guestpostlinks` — checks guest post broker listings
   - `"sitename.com" linkedin OR facebook OR instagram` — checks for real social profiles
   - `"sitename.com" owner OR founder OR "about us"` — checks for real people behind the site

3. **Never use bash/curl/wget** to fetch URLs — only web_fetch and WebSearch.

### Step 3: Evaluate against the PBN indicator checklist

For each site, the subagent must check ALL of these indicators and report findings:

#### Red flags (each one increases PBN risk)

- **Guest post monetization**: Is the site listed on guest post broker platforms
  (Vefogix, GuestPostSale, GuestPostLinks, GuestPostNow, PeoplePerHour, Legiit,
  Fiverr, eBay)? Does it have a "Write for Us" page that's really a paid placement
  service? This is the single strongest PBN indicator — a site that sells dofollow
  backlinks at commodity prices ($2-$120/post) exists primarily for link selling.

- **Anchor text restrictions**: Does the site prohibit commercial anchor text like
  "Best", "Review", "Buy", "Cheap", "Top"? This is a classic PBN link-seller
  restriction designed to avoid triggering Google's spam filters.

- **Missing social profiles**: Does the site have real, active social media accounts
  (Facebook, Twitter/X, LinkedIn, Instagram, Pinterest, YouTube) with genuine
  followers and engagement? Or are they missing, empty, or clearly fake?

- **No real about/team page**: Is there a legitimate about page with named founders,
  team members, company history, real photos? Or is it missing, generic, or filled
  with stock imagery?

- **Anonymous or fake authors**: Are post authors real people with verifiable
  credentials and bios? Or just "admin", "editor", generic names with stock photos,
  or obviously AI-generated bios?

- **No real contact info**: Is there a real business address, phone number, registered
  company? Or just a contact form with a gmail/outlook address?

- **Niche mismatch**: Does the domain name match the content? A domain about bourbon
  publishing home renovation content, or a list-building domain with a garden category,
  suggests an expired domain was repurposed for PBN use.

- **Grab-bag topics**: Does the site cover a coherent niche, or is it a random mix of
  unrelated topics (home improvement + crypto + health + travel)? PBNs often accept
  guest posts on any topic.

- **Generic WordPress theme**: Is it using a cookie-cutter theme like Soledad, SmartMag,
  or other generic multi-purpose themes with no customization? Multiple sites using the
  same theme is a shared footprint — a strong PBN network signal.

- **Thin/AI content**: Is the content original, in-depth, and expert? Or is it thin,
  generic, clearly AI-generated, or rewritten from elsewhere?

- **No real audience**: Are there genuine comments, social shares, or community
  engagement? Or is the site a ghost town with no visible readership?

#### Trust signals (each one decreases PBN risk)

- Named, verifiable founder/team with LinkedIn profiles and professional history
- Active social media with real followers and engagement
- Real business registration, physical address, phone number
- Published in or cited by recognized media outlets
- Original, expert-level content with depth and genuine expertise
- Real customer reviews or testimonials from verifiable sources
- Coherent niche focus matching the domain name
- Custom design/branding (not a generic template)
- Genuine audience engagement (comments, community, social shares)
- No guest post broker listings found

### Step 4: Score and classify each site

Based on the evidence, assign each site:

**PBN Risk Score (1-10)**:
- 1-2: Legitimate site with strong trust signals
- 3-4: Legit but low quality / low authority
- 5-6: Borderline — some concerning signals but not definitive
- 7-8: Likely PBN — multiple red flags, few trust signals
- 9-10: Almost certainly PBN — overwhelming evidence of link selling

**Verdict** (one of):
- **Legit** — Safe to pursue backlinks
- **Legit but low quality** — Acceptable but low editorial authority; verify specific content
- **Borderline** — Proceed with caution; do additional due diligence
- **Likely PBN** — Avoid; multiple PBN indicators present
- **PBN** — Do not acquire links; clear link-selling operation

### Step 5: Present results

For each site, provide a structured block:

```
**sitename.com** — Risk: X/10 — **Verdict**

[2-5 bullet points of key evidence, one line each]
```

After all individual evaluations, provide a summary table:

```
| Site | Risk | Verdict | Safe? |
|------|------|---------|-------|
| ... | .../10 | ... | ✓/✗ |
```

Sort the table with safest sites first, riskiest last.

End with a one-paragraph bottom line: how many are safe, how many to avoid,
and any cross-site patterns you noticed (shared themes, same broker networks,
similar templates suggesting the same operator runs multiple sites).

### Step 6: Cross-reference for network footprints

After individual evaluations, look for patterns across the sites that suggest
they belong to the same PBN network:

- Same WordPress theme across multiple sites
- Same guest post broker listings
- Similar content patterns or posting schedules
- Similar site structure or navigation layout
- Overlapping outbound link targets

If you find shared footprints, call them out explicitly — this is high-value
intelligence because it confirms a coordinated PBN rather than just individual
low-quality sites.

## Important notes

- The guest post broker check (Step 2, search #2) is the highest-signal
  investigation. A site openly selling dofollow backlinks through broker
  platforms is the clearest PBN indicator, regardless of what other trust
  signals it might have. A site with a real founder but also selling $5
  guest posts on Vefogix is still a PBN risk.

- Domain age and DA (Domain Authority) alone don't determine legitimacy.
  PBNs often acquire expired domains with existing DA specifically to sell
  higher-priced guest posts.

- The user's language matters. If they say "evaluate" or "check", they want
  the full analysis. If they say "is this legit?", they might want a quicker
  verdict — but still do the full investigation; just present it more concisely.

- Always present findings as evidence-based assessments, not speculation.
  Every claim should be backed by something you found (or didn't find).
