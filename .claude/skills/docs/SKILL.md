---
name: docs
description: Generate app documentation pages (landing page, privacy policy, support, terms of service) for iOS apps. Use when the user asks about creating or updating app docs, legal pages, or marketing pages.
allowed-tools: Read, Grep, Glob, Bash, WebSearch, WebFetch
argument-hint: [app name or project path]
---

You generate documentation pages for iOS apps. Before starting, always ask the user:

1. **Which app/project** to generate docs for
2. **Which pages** to generate (or all four)
3. **Developer/company info** — name, address, email, website (required for legal pages)
4. **Any existing docs** to update rather than create from scratch

## Pages

Generate any combination of these four pages:

### 1. General Landing Page

App marketing page. Structure:

- App name + tagline
- Key features (3-5 bullets)
- How it works (step-by-step)
- Screenshots / visual descriptions (placeholder references)
- Download CTA (App Store link)
- Footer with links to Privacy Policy, Terms of Service, Support

### 2. Privacy Policy

Must cover:

- What data is collected (personal info, usage data, device info, photos, etc.)
- How data is used
- Third-party services and SDKs (analytics, ads, crash reporting)
- Data retention and deletion
- User rights (access, correction, deletion requests)
- Children's privacy (COPPA compliance if applicable)
- Contact information for privacy inquiries
- Effective date and update policy
- Jurisdiction-specific sections if needed (GDPR, CCPA)

### 3. Support Page

Structure:

- Contact email for support
- FAQ section (common questions about the app)
- Troubleshooting steps for common issues
- How to request account/data deletion
- Response time expectations
- Links to Privacy Policy and Terms of Service

### 4. Terms of Service

**MUST comply with Apple's Minimum Terms (EULA) requirements.** All 10 clauses below are mandatory:

#### Apple Required Clauses

1. **Acknowledgement** — The agreement is between the developer and the end-user only, not Apple. Must not conflict with Apple Media Services Terms and Conditions.

2. **Scope of License** — License must be limited to a non-transferable license to use the app on Apple devices owned/controlled by the end-user, per Usage Rules in Apple Media Services T&C. Must account for Family Sharing and volume purchasing.

3. **Maintenance and Support** — Developer is solely responsible for maintenance and support. Apple has no obligation to provide support.

4. **Warranty** — Developer bears full warranty responsibility. If the app fails to meet warranty, Apple's maximum obligation is a purchase price refund. Apple has no other warranty obligation.

5. **Product Claims** — Developer handles all claims including: product liability, failure to meet legal/regulatory requirements, consumer protection, and claims involving HealthKit/HomeKit frameworks.

6. **Intellectual Property Rights** — Developer is responsible for defending against third-party IP infringement claims involving the app.

7. **Legal Compliance** — End-user must represent they are not in an embargoed country and not on any US government prohibited parties list.

8. **Developer Contact Information** — Must include developer name, address, phone number, and email for user inquiries and complaints.

9. **Third Party Terms** — Must reference any applicable third-party agreements users must comply with.

10. **Third Party Beneficiary** — Must state that Apple and Apple's subsidiaries are third-party beneficiaries of the EULA, with the right to enforce it against end-users.

Reference: https://www.apple.com/legal/internet-services/itunes/dev/minterms/

#### Additional Terms to Include

- Acceptable use / prohibited conduct
- Subscription terms (if applicable — pricing, billing, cancellation, refunds)
- In-app purchases (if applicable)
- User-generated content policy (if applicable)
- Limitation of liability
- Indemnification
- Governing law and dispute resolution
- Termination conditions
- Modification of terms and notification process
- Effective date

## Output Format

Generate each page as clean **Markdown** ready to paste into Notion, a website CMS, or convert to HTML. Use clear headings, numbered/bulleted lists, and bold for emphasis.

Each page should include:
- A `Last Updated: [date]` line at the top
- The app name and developer name consistently
- Placeholder markers like `[APP_STORE_LINK]`, `[SCREENSHOT_1]` where dynamic content goes

## Process

1. **Ask** which app and which pages to generate
2. **Analyze** the project — read the codebase/config to understand what data the app collects, what third-party SDKs are used, whether there are subscriptions/IAPs, etc.
3. **Draft** all requested pages
4. **Present** for review — flag any sections that need the user's input (e.g., specific data collection practices, pricing details)
5. **Revise** based on feedback

## Response Style

- Professional legal tone for Privacy Policy and Terms of Service
- Friendly marketing tone for Landing Page
- Helpful and clear tone for Support Page
- Always flag assumptions explicitly
- Never fabricate specific data practices — ask if unsure