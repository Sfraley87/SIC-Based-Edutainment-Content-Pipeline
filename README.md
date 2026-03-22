# SIC-Based Edutainment Content Pipeline
### Industry-targeted AI content engine — persona generation, trend sourcing, and automated publishing

![n8n](https://img.shields.io/badge/Built%20with-n8n-orange?style=flat-square)
![OpenAI](https://img.shields.io/badge/LLM-OpenAI-412991?style=flat-square)
![Blotato](https://img.shields.io/badge/Publishing-Blotato-1DA1F2?style=flat-square)
![Reddit](https://img.shields.io/badge/Trends-Reddit%20API-FF4500?style=flat-square)
![Status](https://img.shields.io/badge/Status-Template-gray?style=flat-square)

---

## Overview

This pipeline generates and publishes industry-specific educational content daily — fully automated, from audience research to social post.

The core concept: target any industry using its **SIC code**, pull real company data to build an accurate audience persona, surface trending conversations from that industry's Reddit community, and use that context to generate content that actually resonates — then push it to LinkedIn (or any supported platform) via Blotato.

**Change the SIC code. Change the industry. Everything else adapts.**

---

## What It Does

1. **Identifies the target industry** via SIC code (e.g. `8021` = Dentists)
2. **Pulls real business data** from OpenCorporates API to ground the persona in actual companies
3. **Builds a detailed audience persona** using OpenAI — pain points, goals, content tone, key topics
4. **Fetches trending discussions** from the industry's subreddit in real time
5. **Generates edutainment content** — educational + entertaining — tailored to that persona and those trends
6. **Publishes automatically** via Blotato to LinkedIn (or any configured platform)
7. **Logs the result** with submission ID and timestamp

---

## Architecture

```
Daily Schedule Trigger (8:00 AM)
        │
        ▼
Campaign Config              ← SIC code, industry, product brief, platform
        │
        ▼
Scrape Businesses (SIC)      ← OpenCorporates API — real company data for the target industry
        │
        ▼
Build Audience Persona       ← OpenAI — generates persona JSON (pain points, goals, tone, topics)
        │
        ▼
Parse Persona JSON           ← Normalizes output; fallback persona if LLM returns malformed JSON
        │
        ▼
Fetch Trending Topics        ← Reddit hot posts from industry subreddit (live signal)
        │
        ▼
Generate Edutainment Content ← OpenAI — combines persona + trends → platform-ready post
        │
        ▼
Extract Post Text            ← Strips to clean post copy + metadata
        │
        ▼
Post via Blotato             ← Publishes to LinkedIn (or configured platform)
        │
        ▼
Log Result                   ← Records submission ID + completion timestamp
```

---

## Key Design Decisions

**SIC code as the targeting primitive** — SIC codes give you precise, standardized industry classification. Instead of fuzzy keyword targeting, this pipeline uses the same taxonomy accountants, regulators, and enterprise sales teams use. Switch one value and the entire pipeline retargets to a new vertical.

**Real company data for persona grounding** — Most AI content pipelines generate personas from thin air. This one pulls actual business records from OpenCorporates first, giving the LLM real organizational context before building the persona. The output is more accurate and less generic.

**Live Reddit signal** — Trend data is fetched fresh each run. The content generated on Monday reflects what that industry is actually talking about on Monday — not a static topic list from six months ago.

**Fallback persona handling** — The `Parse Persona JSON` node includes a hardcoded fallback in case the LLM returns malformed JSON. The pipeline never breaks on a bad LLM response.

---

## Configuration

All targeting is controlled from a single `Campaign Config` node — no digging through the workflow to swap industries:

| Field | Description | Example |
|-------|-------------|---------|
| `sic_code` | Industry SIC classification code | `8021` |
| `industry_name` | Human-readable label | `Dentists` |
| `product_brief` | What you're promoting | Your product/service description |
| `blotato_api_key` | Blotato API credential | `YOUR_BLOTATO_API_KEY` |
| `blotato_account_id` | Blotato account target | `YOUR_BLOTATO_ACCOUNT_ID` |
| `target_platform` | Publishing destination | `linkedin` |

### Common SIC Codes to Target

| SIC | Industry |
|-----|----------|
| `8021` | Dentists |
| `7011` | Hotels & Motels |
| `5511` | Auto Dealers |
| `8011` | Medical Doctors |
| `7389` | Business Services |
| `5812` | Restaurants |
| `8049` | Chiropractors |
| `1711` | Plumbing & HVAC |

Full SIC list: [siccode.com](https://siccode.com)

---

## Setup

### Prerequisites
- n8n instance
- OpenAI API key
- Blotato account + API key
- No auth required for OpenCorporates (public endpoint) or Reddit (public JSON feed)

### Credentials Needed

| Credential | Node |
|------------|------|
| OpenAI API key | `Build Audience Persona`, `Generate Edutainment Content` |
| Blotato API key | `Campaign Config` → passed to `Post via Blotato` header |

### To Deploy
1. Import the workflow JSON into n8n
2. Add your OpenAI credential to both OpenAI nodes
3. Fill in `blotato_api_key` and `blotato_account_id` in `Campaign Config`
4. Set your `sic_code`, `industry_name`, and `product_brief`
5. Activate

---

## Extending This

- **Multi-industry** — wrap in a loop over a SIC code list to run parallel campaigns for multiple verticals
- **Image generation** — add a DALL·E or fal.ai node to generate a matching visual before posting
- **A/B testing** — generate two post variants and split-test via Blotato
- **CRM sync** — pipe the generated content and target company list into HubSpot or a Google Sheet for sales alignment
- **Platform expansion** — Blotato supports Twitter/X, Facebook, Instagram — change `target_platform` to diversify distribution

---

## About

Built by **Shaun** — AI Automation Architect at [Synelium](https://synelium.com)

Synelium designs and deploys AI-powered automation infrastructure for mid-market businesses. This pipeline is part of a broader content intelligence system built for B2B companies that need industry-specific thought leadership at scale — without a content team.

→ More projects:https://github.com/Sfraley87)  
→ Connect: www.linkedin.com/in/shaun-fraley-41a0b9132

