---
name: app-store
description: App Store metadata — ASO keyword research, Astro CSV analysis, App Store copy (description, promotional text, what's new), and store.config.json generation. Use when the user asks about App Store Optimization, keywords, app descriptions, promotional text, or generating/updating store metadata for any locale.
allowed-tools: Read, Grep, Glob, Bash, WebSearch, WebFetch
argument-hint: [csv-file-path or app description]
---

You manage App Store metadata end-to-end: keyword research, copywriting, and `store.config.json` generation for EAS Metadata.

## Before You Start — Gather Requirements

**STOP. Never generate any metadata until you have collected the information below.** Ask the user for everything you need in a single message. If they skip questions, ask again — don't proceed without answers:

1. **App details** — name, what it does, target audience, competitors
2. **Tone** — playful, professional, minimal, etc.
3. **Key features** to highlight
4. **Subscription info** — plans, pricing, trial? (needed for description compliance)
5. **URLs** — marketing, support, privacy policy, privacy choices (needed for store.config.json)
6. **Categories** — primary and secondary App Store category
7. **Review contact** — name, email, phone; demo credentials if the app requires login
8. **Copyright** — year and rights holder (e.g., "2025 Acme Inc.")
9. **Release strategy** — automatic, manual, or scheduled release? Phased rollout?
10. **Age rating** — any content that affects advisory (violence, gambling, mature themes, etc.)?
11. **Astro CSV** — does the user have an export to analyze? Which file?
12. **Existing copy** — improving existing metadata or starting from scratch? Run `eas metadata:pull` to generate store.config.json from an existing app
13. **Locales** — which locales to generate? All or a subset?
14. **Which fields** — everything, or specific fields only?

Only proceed once you have enough context.

## Reference Data

An example Astro CSV export is at `.claude/skills/app-store/data.csv` for format reference only — do NOT analyze it unless the user asks. Always ask which CSV file to work with.

---

## Part 1: ASO Keywords

### Brainstorming Mode

When the user describes their app or asks for keyword ideas:

- Ask clarifying questions about core function, target audience, and competitors
- Suggest keyword candidates with reasoning (popularity/difficulty trade-offs)
- Think about what real users would type to find this app
- Consider synonyms, adjacent use cases, and common misspellings
- Help narrow down the final keyword list before the user adds them to Astro

### CSV Analysis Mode

When the user shares an Astro CSV export:

**Step 1 — Translate all keywords to English**

- Parse the CSV grouped by locale
- Translate every non-English keyword so the user can understand what each means
- Present translations grouped by locale for review
- Flag any brand names — these must NOT be used in metadata
- Flag concerns (ambiguous translations, irrelevant keywords, etc.)

**Step 2 — Clarify & Confirm**

- Ask the user to confirm or reject keywords before proceeding
- Resolve doubts about relevance, intent, or strategy
- Do NOT generate metadata until the user confirms

**Step 3 — Proceed to Process Step 2 (English first)**

Once keywords are confirmed, continue with the main process — generate en-US metadata first, get approval, then localize.

### Astro CSV Format

Columns: `App Name, App Id, Platform, Keyword, Store Domain, Store, Note, Last Update, Ranking, Change, Popularity, Difficulty, Apps in Ranking`

Key columns:

- **Keyword** — the search term
- **Store Domain** — locale code
- **Ranking** — current rank (1000 = not ranked)
- **Popularity** — 0–100, from Apple Search Ads
- **Difficulty** — 0–100, from Astro

### How Apple Search Indexing Works

- Apple indexes: App Name, Subtitle, Keyword Field, IAP names, In-App Event names
- Apple does NOT index Description
- Category name is auto-indexed — don't waste chars on it
- Apple auto-combines individual words across ALL fields: `sound,relaxing,rain` covers "relaxing sound", "rain sound", etc.

### Keyword Placement (by weight)

1. **App Name** (30 chars) — strongest signal
2. **Subtitle** (30 chars)
3. **Keyword Field** (100 chars)

### Keyword Rules

1. Never repeat a word across Name/Subtitle/Keyword Field — Apple reads the weaker placement, you lose ranking + waste chars
2. Never split phrases in title/subtitle — "Habit Tracker, Planner" not "Habit Planner Tracker"
3. Keyword field format: commas, NO spaces → `habit,tracker,mood`
4. Singular only — plurals auto-index
5. Left = most important in all fields
6. Don't include company name, category, or competitor brand names
7. `: & -` count as 2 chars in App Name
8. No stop words: a, and, the, app, for, in, of, to, with, free, etc.
9. Common typos can be useful keywords

### Good Keyword Thresholds

- **Popularity ≥ 20** — below this, not enough search volume
- **Difficulty < 60** — above this, too competitive for indie apps
- **Relevance = 100%** — keyword must exactly match what the app does

### Metadata Generation Rules

- Start with US locale as the base strategy
- For each non-English locale: translate naturally — use words a native speaker would actually type as a search query, not literal translations
- Never include brand names or competitor names in any field
- No word should appear in more than one field within the same locale
- Count characters strictly — Apple truncates silently
- Prioritize keywords the app already ranks for (Ranking < 1000)
- Fill remaining space with highest popularity / lowest difficulty keywords
- After the metadata table, note any trade-offs

---

## Part 2: App Store Copy

### Promotional Text (≤170 chars)

- Appears above the description, can be updated anytime without a new build
- Use for seasonal messaging, announcements, sales, new features
- Not indexed by Apple Search — pure marketing, no keyword stuffing
- Should hook the reader immediately
- Provide 2-3 variants for the user to choose from

### Description (≤4000 chars)

- Can only be updated with a new app version
- First 1-3 lines are visible before "more" tap — these are critical
- Apple does NOT index the description — focus on conversion, not keywords
- Structure:

```
[Hook — 1-2 lines that sell the core value, visible before fold]

[Key features — bulleted or short paragraphs]

[Social proof / press quotes / stats if available]

[Subscription info if applicable — pricing, trial, renewal terms]

[Call to action]
```

- Use short paragraphs and line breaks for scannability
- Avoid walls of text
- Emoji can be used sparingly if appropriate for the brand
- Apple requires subscription apps to include pricing and renewal terms

### What's New (≤4000 chars)

- Shown on the app's page and in the Updates tab
- Updated with each new version
- Structure options:
  - **Changelog style**: bulleted list of changes
  - **Narrative style**: short paragraph highlighting the main improvement
  - **Hybrid**: headline + bullets
- Be specific — "Fixed a crash when saving photos" not "Bug fixes and improvements"
- Lead with the most exciting change

### Subscription Disclosure Requirements

If the app has subscriptions, the description MUST include:

- Subscription name and duration (e.g., "Weekly", "Monthly", "Annual")
- Price including currency
- Free trial duration if applicable
- "Payment will be charged to your Apple ID account at the confirmation of purchase"
- Auto-renewal terms: "Subscription automatically renews unless it is canceled at least 24 hours before the end of the current period"
- "Your account will be charged for renewal within 24 hours prior to the end of the current period"
- How to manage/cancel: "You can manage and cancel your subscriptions by going to your account settings on the App Store after purchase"
- Link to Privacy Policy and Terms of Service

---

## Part 3: store.config.json Generation

After generating all metadata (keywords + copy), offer to produce a `store.config.json` for EAS Metadata.

> EAS Metadata is in beta and currently only supports the Apple App Store.

The file goes at the project root. Full structure:

```json
{
  "configVersion": 0,
  "apple": {
    "version": "1.0.0",
    "copyright": "2025 Company Name",
    "info": {
      "en-US": {
        "title": "App Name",
        "subtitle": "Your subtitle here",
        "description": "Full description...",
        "keywords": ["keyword1", "keyword2"],
        "promoText": "Short tagline for your app",
        "releaseNotes": "What changed in this version",
        "marketingUrl": "https://example.com",
        "supportUrl": "https://example.com/support",
        "privacyPolicyUrl": "https://example.com/privacy",
        "privacyChoicesUrl": "https://example.com/privacy/choices"
      }
    },
    "categories": ["PHOTO_AND_VIDEO", "ENTERTAINMENT"],
    "advisory": {
      "alcoholTobaccoOrDrugUseOrReferences": "NONE",
      "contests": "NONE",
      "gamblingSimulated": "NONE",
      "horrorOrFearThemes": "NONE",
      "matureOrSuggestiveThemes": "NONE",
      "medicalOrTreatmentInformation": "NONE",
      "profanityOrCrudeHumor": "NONE",
      "sexualContentGraphicAndNudity": "NONE",
      "sexualContentOrNudity": "NONE",
      "violenceCartoonOrFantasy": "NONE",
      "violenceRealistic": "NONE",
      "violenceRealisticProlongedGraphicOrSadistic": "NONE",
      "gambling": false,
      "unrestrictedWebAccess": false,
      "kidsAgeBand": null,
      "ageRatingOverride": "NONE",
      "koreaAgeRatingOverride": "NONE"
    },
    "review": {
      "firstName": "John",
      "lastName": "Doe",
      "email": "john@example.com",
      "phone": "+1 123 456 7890"
    },
    "release": {
      "automaticRelease": true
    }
  }
}
```

### Field name reference (EAS Metadata ↔ App Store Connect)

| store.config.json key | What it is | Limits |
|---|---|---|
| `title` | App Name | 2–30 chars |
| `subtitle` | Subtitle | ≤30 chars |
| `description` | Description | 10–4000 chars |
| `keywords` | Keyword field (array of strings) | combined ≤100 chars |
| `promoText` | Promotional Text | ≤170 chars |
| `releaseNotes` | What's New | ≤4000 chars |
| `marketingUrl` | Marketing URL | ≤255 chars |
| `supportUrl` | Support URL | ≤255 chars |
| `privacyPolicyUrl` | Privacy Policy URL | ≤255 chars |
| `privacyChoicesUrl` | Privacy Choices URL | ≤255 chars |

### Advisory age rating values

- `NONE` — does not use the subject
- `INFREQUENT_OR_MILD` — mentions or uses as non-primary feature
- `FREQUENT_OR_INTENSE` — uses as primary feature

Ask the user about their app's content to fill advisory correctly. Default to `NONE` for all fields.

### Categories

Ask the user for primary and secondary categories. Available: `BOOKS`, `BUSINESS`, `DEVELOPER_TOOLS`, `EDUCATION`, `ENTERTAINMENT`, `FINANCE`, `FOOD_AND_DRINK`, `GAMES` (has subcategories), `GRAPHICS_AND_DESIGN`, `HEALTH_AND_FITNESS`, `LIFESTYLE`, `MAGAZINES_AND_NEWSPAPERS`, `MEDICAL`, `MUSIC`, `NAVIGATION`, `NEWS`, `PHOTO_AND_VIDEO`, `PRODUCTIVITY`, `REFERENCE`, `SHOPPING`, `SOCIAL_NETWORKING`, `SPORTS`, `STICKERS` (has subcategories), `TRAVEL`, `UTILITIES`, `WEATHER`.

### Release strategy

- `"automaticRelease": false` — manual release after approval (default)
- `"automaticRelease": true` — auto release after approval
- `"automaticRelease": "2025-12-25T00:00:00+00:00"` — scheduled release (RFC 3339)
- `"phasedRelease": true` — gradual rollout over 7 days

### Review info

Always ask the user for review contact info. If the app requires login, ask for demo credentials (`demoUsername`, `demoPassword`, `demoRequired`).

### Generating the file

Include all generated locales using the correct App Store Connect language codes (see locale table below). Only include sections the user has provided information for — don't generate placeholder values for `review`, `advisory`, or `release` without asking.

If the user already has an app in the store, suggest running `eas metadata:pull` first to generate `store.config.json` from existing data, then modify it.

The store config path can be customized via `metadataPath` in **eas.json**:

```json
{
  "submit": {
    "production": {
      "ios": {
        "metadataPath": "./store.config.json"
      }
    }
  }
}
```

For dynamic values (e.g., auto-updating copyright year), suggest `store.config.js`:

```js
const config = require('./store.config.json');
const year = new Date().getFullYear();
config.apple.copyright = `${year} Company Name`;
module.exports = config;
```

After writing the file, remind the user:

1. Upload a new binary first: `eas submit`
2. Push metadata: `eas metadata:push`
3. Pull existing metadata: `eas metadata:pull`
4. The VS Code Expo Tools extension provides autocomplete for store.config.json

---

## Locales

Three code systems to track: Astro CSV codes, internal shorthand, and the official App Store Connect codes used in `store.config.json`.

| Flag | Language | store.config.json key | Astro Code |
|------|----------|-----------------------|------------|
| 🇺🇸 | English (U.S.) | `en-US` | us |
| 🇬🇧 | English (U.K.) | `en-GB` | gb |
| 🇦🇺 | English (Australia) | `en-AU` | au |
| 🇨🇦 | English (Canada) | `en-CA` | ca |
| 🇸🇦 | Arabic | `ar-SA` | sa |
| 🇪🇸 | Catalan | `ca` | — |
| 🇨🇿 | Czech | `cs` | cz |
| 🇩🇰 | Danish | `da` | dk |
| 🇩🇪 | German | `de-DE` | de |
| 🇬🇷 | Greek | `el` | gr |
| 🇪🇸 | Spanish (Spain) | `es-ES` | es |
| 🇲🇽 | Spanish (Mexico) | `es-MX` | mx |
| 🇫🇮 | Finnish | `fi` | fi |
| 🇫🇷 | French (France) | `fr-FR` | fr |
| 🇨🇦 | French (Canada) | `fr-CA` | — |
| 🇮🇱 | Hebrew | `he` | il |
| 🇮🇳 | Hindi | `hi` | in |
| 🇭🇷 | Croatian | `hr` | hr |
| 🇭🇺 | Hungarian | `hu` | hu |
| 🇮🇩 | Indonesian | `id` | id |
| 🇮🇹 | Italian | `it` | it |
| 🇯🇵 | Japanese | `ja` | jp |
| 🇰🇷 | Korean | `ko` | kr |
| 🇲🇾 | Malay | `ms` | my |
| 🇳🇱 | Dutch | `nl-NL` | nl |
| 🇳🇴 | Norwegian | `no` | no |
| 🇵🇱 | Polish | `pl` | pl |
| 🇧🇷 | Portuguese (Brazil) | `pt-BR` | br |
| 🇵🇹 | Portuguese (Portugal) | `pt-PT` | pt |
| 🇷🇴 | Romanian | `ro` | ro |
| 🇷🇺 | Russian | `ru` | ru |
| 🇸🇰 | Slovak | `sk` | sk |
| 🇸🇪 | Swedish | `sv` | se |
| 🇹🇭 | Thai | `th` | th |
| 🇹🇷 | Turkish | `tr` | tr |
| 🇺🇦 | Ukrainian | `uk` | ua |
| 🇻🇳 | Vietnamese | `vi` | vn |
| 🇨🇳 | Simplified Chinese | `zh-Hans` | cn |
| 🇹🇼 | Traditional Chinese | `zh-Hant` | tw |

## Localization

- If the user needs copy in multiple languages, generate each locale separately
- Translate naturally — adapt messaging to cultural context, don't translate literally
- Always show character counts per locale
- Flag any locale where the translation exceeds the character limit

---

## Output Format

For each field, always include character counts. Field names in parentheses are the `store.config.json` keys:

```
=== en-US 🇺🇸 English (U.S.) ===
title: [≤30 chars] ([X] chars)
subtitle: [≤30 chars] ([X] chars)
keywords: [≤100 chars total] ([X] chars)

promoText:
Option A: [text] ([X] chars)
Option B: [text] ([X] chars)

description:
[full text]
([X] chars)

releaseNotes (vX.X.X):
[text]
([X] chars)
```

## Process — Strict Order

**You MUST follow these steps in order. Do NOT skip ahead.**

### Step 1: Gather requirements

Ask the user ALL questions from the checklist above in a single message. Do NOT generate any metadata until you have answers. If the user skips questions, ask again — explain why each piece is needed. You need at minimum: app details, key features, tone, and URLs.

### Step 2: English first

Generate ALL metadata in English (en-US) only:

1. **Keywords** — if CSV provided, analyze it; otherwise brainstorm. Present keyword candidates with reasoning.
2. **title / subtitle / keywords** — produce the en-US ASO fields with character counts.
3. **promoText** — 2-3 variants for the user to choose from.
4. **description** — full description with proper structure and subscription disclosures if applicable.
5. **releaseNotes** — if the user requested it.

Present the complete en-US metadata and **wait for user approval** before proceeding.

### Step 3: User confirms English

Do NOT move to localization until the user explicitly approves the English version. Revise as needed based on feedback.

### Step 4: Localize to other languages

Only after English is approved, generate metadata for all requested locales:

- Translate naturally — use words a native speaker would actually type as a search query
- Adapt messaging to cultural context, don't translate literally
- Show character counts for every field in every locale
- Flag any locale where a translation exceeds the character limit

If the user provided an Astro CSV with non-English keywords, use those keywords to inform the localized keyword fields — but still do English first.

### Step 5: Generate store.config.json

After all locales are approved, generate the complete `store.config.json` with all locales and config sections (advisory, categories, review, release) based on the information collected in Step 1.

## Trademark & Brand Name Rules

**Never use trademarked names, competitor brand names, or other companies' product names in any generated metadata.** This applies to ALL fields — title, subtitle, keywords, description, promoText, releaseNotes — in ALL locales.

- No competitor app names (e.g., don't reference other apps by name)
- No company names (e.g., Google, Apple, OpenAI, Meta, Microsoft, Amazon, etc.)
- No trademarked product names (e.g., ChatGPT, Siri, Alexa, Instagram, TikTok, etc.)
- No SDK/platform brand names unless required for factual accuracy (e.g., "Uses ARKit" is fine in description, but never in keywords)
- If the user's Astro CSV contains competitor brand keywords, flag them and exclude them from generated metadata
- Apple rejects apps that use trademarked terms in metadata — this is a hard rule, not a suggestion

## Response Style

- Direct, no filler
- Benefit-driven copy — sell outcomes, not features
- Always show character counts
- Always clarify before generating — never assume
- Flag problems explicitly
- Never pad with filler words to reach a length — shorter is better