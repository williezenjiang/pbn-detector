# pbn-detector
A Skill that evaluates websites for Private Blog Network (PBN) risk before you acquire backlinks from them. Drop in a list of URLs and get a structured risk report with scores, verdicts, and evidence.

## What it does

PBNs are networks of low-quality blogs created to sell backlinks and manipulate Google rankings. Getting links from them risks Google penalties. This skill automates the due diligence that SEO professionals do manually — checking each site for red flags like guest post broker listings, missing social profiles, anonymous authors, and niche mismatches.

**Input:** A list of URLs (handles 10+ sites in bulk via parallel evaluation)

**Output:** For each site:
- PBN risk score (1-10)
- Verdict (Legit / Legit but low quality / Borderline / Likely PBN / PBN)
- Evidence bullets
- Summary table sorted by risk
- Cross-site network footprint analysis

## Example

```
Check these backlink prospects for PBN risk:

https://www.bhg.com/
https://upgradedens.com/
https://www.gardendesign.com/
```

**Result:**

| Site | Risk | Verdict | Safe? |
|------|------|---------|-------|
| bhg.com | 1/10 | Legit | ✓ |
| gardendesign.com | 2/10 | Legit | ✓ |
| upgradedens.com | 9/10 | PBN | ✗ |

## What it checks

### Red flags
- Guest post broker listings (Vefogix, GuestPostSale, GuestPostLinks, PeoplePerHour, Legiit, Fiverr)
- Anchor text restrictions ("no Best/Review/Buy/Cheap/Top" = classic PBN rule)
- Missing or fake social profiles
- No real about/team page or contact info
- Anonymous or fake authors
- Domain name / content niche mismatch
- Generic WordPress themes (Soledad, SmartMag)
- Thin or AI-generated content
- No real audience engagement

### Trust signals
- Named, verifiable founder/team with LinkedIn
- Active social media with real followers
- Real business registration and address
- Published in recognized media outlets
- Original expert content
- No guest post broker listings

## Installation

### Option 1: Install the `.skill` file
1. Download `pbn-detector.skill` from this repo
2. Open Claude Desktop (Cowork mode)
3. Drag the `.skill` file into the chat — click **Save skill** when prompted

### Option 2: Manual install
1. Copy `SKILL.md` into your Claude skills directory
2. The skill will auto-trigger when you mention PBN detection, backlink audits, or ask to evaluate websites

## Requirements

- Claude Desktop with Cowork mode (for parallel bulk evaluation via subagents)
- Also works in Claude.ai (evaluates sites sequentially instead of in parallel)
- No external API keys needed — uses web search and page fetching built into Claude

## How it works

1. Groups URLs into batches of 4-5
2. Dispatches parallel subagents — each one fetches the site, checks about/contact pages, and searches for broker listings
3. Evaluates each site against the full red flag / trust signal checklist
4. Scores and classifies each site
5. Cross-references sites for shared PBN network footprints (same theme, same brokers)
6. Presents a structured report with summary table

## License

MIT

## Author

Willie Jiang — [@williezenjiang](https://github.com/williezenjiang)
