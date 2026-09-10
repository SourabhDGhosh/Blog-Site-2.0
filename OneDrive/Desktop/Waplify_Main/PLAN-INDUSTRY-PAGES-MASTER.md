# Industry Pages: Master Build Specification

**Status:** FINAL. This is the single source for building all 18 industry pages.
**Created:** 2026-08-21
**Supersedes for build purposes:** `PLAN-A7-EXECUTION.md`, `PLAN-INDUSTRY-PAGES-FINAL.md`, `PLAN-INDUSTRY-PAGE-STRUCTURE.md`. Those stay as the reasoning trail.
**Evidence:** [`A7-KEYWORDS-WITH-VOLUME.md`](A7-KEYWORDS-WITH-VOLUME.md), [`PLAN-A7-FULL-TAXONOMY-KEYWORD-REPORT.md`](PLAN-A7-FULL-TAXONOMY-KEYWORD-REPORT.md)
**Priority context:** [`PLAN-SEO-PRIORITY.md`](PLAN-SEO-PRIORITY.md)

---

## 0. How to use this file

Read §1 and §2 first. They are closed decisions.

- **Developer:** §3 architecture, §4 template, §9 schema, §11 linking, §12 QA.
- **Writer / owner:** §5 content rules, §6 AEO rules, §8 per-industry build sheets.
- **AI session:** §14 handoff prompt. Read it before anything else.

Three owner decisions are still open. They are listed in §13 and block the first
publish, not the build. Start building; settle them before page one goes live.

---

## 1. Locked decisions

| # | Decision | Value |
|---|---|---|
| 1.1 | Page count | 18. Ten Tier 1, eight Tier 2 |
| 1.2 | Purpose | Navigation, sales, and cheap testing of the template family. Not the traffic play |
| 1.3 | URL | `/industries/<slug>/`, hub at `/industries/`. See §13.1, still reversible |
| 1.4 | Publishing | Manual. Owner publishes 2 at a time |
| 1.5 | Cadence | 2 pages every 3 days |
| 1.6 | Copy | Owner brief, AI draft, owner review. Review is the release gate |
| 1.7 | Nav | Header megamenu holds the Tier 1 ten plus "See all industries". Tier 2 in hub and footer |
| 1.8 | Word budget | Tier 1: 700 to 900. Tier 2: 400 to 500 |
| 1.9 | Uniqueness floor | 40% minimum unique body copy between any two pages. Below 30% is a hard stop |
| 1.10 | Template structure | One spine, optional modules per industry. §4 |
| 1.11 | Hero imagery | Phone mockup showing a real WhatsApp conversation. No fabricated Waplify dashboard screens |
| 1.12 | Schema | BreadcrumbList, FAQPage, SoftwareApplication, ItemList, HowTo. §9 |

### Why these pages exist, stated plainly

Measurement across 1,585 taxonomy terms and 9,510 keyword cells found roughly
750 genuine monthly searches for the entire industry family. 314 of 321 L1
industries return zero.

These pages are worth building anyway because:

1. The header megamenu needs somewhere to point, and every competitor has one.
2. A visitor arriving on `whatsapp business api` (18,100/mo) who runs a salon
   converts better after seeing that Waplify understands salons.
3. Sales needs a link to send when a prospect asks "do you work with gyms?".
4. The template section on each page tests the next content family for the cost
   of a section rather than a page.

**Do not judge these pages on impressions.** If a future session proposes
expanding the set "for SEO", that proposal is already answered. Say no.

---

## 2. Three things that must not ship

### 2.1 No integration logo wall

Waplify ships **two** connectors. Verified at
[`site/src/data/integrations/index.ts:30`](../site/src/data/integrations/index.ts):
`INTEGRATIONS = [indiamart, googleSheets]`, plus a REST API and outbound webhooks.

A strip showing Shopify, Razorpay, Shiprocket and Zoho as if they were built
integrations is a false claim and a trademark risk. It shipped once already and
was replaced on 2026-07-31 (CLAUDE.md §15.3 rule 3).

**Approved wording for the six-card grid:**

| Card | Label | Line |
|---|---|---|
| IndiaMART | Native connector | Leads arrive as contacts, ready for a flow |
| Google Sheets | Native connector | Read and write rows from inside a flow |
| Shopify | Connect by webhook | Any store event can start a flow |
| WooCommerce | Connect by webhook | Order and customer events trigger messages |
| REST API | Built in | Contacts, campaigns, messages, templates |
| Zapier, Make, n8n | Connect by webhook | Anything that sends an HTTP request |

### 2.2 No unsourced statistics

Every number in a stats band needs a source shown on the page, or it comes out.
No exceptions, including "98% open rate", which is the most-repeated unsourced
number in this industry.

AI search engines quote numbers with the brand attached. A fabricated statistic
becomes a fabricated citation that cannot be retracted.

### 2.3 No invented customers

A testimonial block ships only on industries where a real, named, consenting
customer exists. Otherwise the block is omitted for that industry and the space
goes to the template section.

An empty or fake logo wall is worse than no block.

---

## 3. Architecture

### 3.1 The core mechanic

**One registry entry lights up every surface at once:** the page, the header
megamenu, the footer column, the `/industries/` hub grid, the related-industries
strip on siblings, `sitemap-index.xml`, `llms.txt` and `rss.xml`.

Nothing is maintained by hand, so nothing rots, and no page can go live orphaned.
This is the proven `/integrations/` pattern. Copy it exactly.

### 3.2 Files

**New:**

| File | Purpose |
|---|---|
| `site/src/data/industries/types.ts` | Content model, including the optional-module flags |
| `site/src/data/industries/index.ts` | Registry, hub content, `related()` helper |
| `site/src/data/industries/<slug>.ts` | One per industry, 18 total |
| `site/src/components/sections/IndustryPage.astro` | The one template |
| `site/src/components/sections/industry/AnswerBlock.astro` | §6.1 |
| `site/src/components/sections/industry/TemplateLibrary.astro` | §7 |
| `site/src/components/sections/industry/SubIndustryGrid.astro` | §4, all panels in DOM |
| `site/src/components/sections/industry/ComparisonTable.astro` | §6.3 |
| `site/src/pages/industries/index.astro` | Hub |
| `site/src/pages/industries/[slug].astro` | One dynamic route for all 18 |

**Edited:** `navigation.ts`, `siteIndex.ts`, `pageDates.ts`.

Sitemap needs no edit. It derives from built routes.

### 3.3 `related()` helper

Each module declares a `sector`. `related(slug)` returns up to three other **live**
industries in the same sector, falling back to the next registry entries when a
sector is thin. Never returns itself. Never returns an unpublished page, because
unpublished pages are not in the registry.

### 3.4 Sub-industry pages, later

`/industries/<industry>/<niche>/` is reserved. Do not build any yet. A niche earns
a page only when its parent page shows impressions for that niche. This is why
§13.1 recommends the `/industries/` URL shape.

---

## 4. The page template

### 4.1 Fixed spine, every page

| # | Block | Component | Notes |
|---|---|---|---|
| 1 | Breadcrumb | existing | Home > Industries > this industry |
| 2 | Hero | `FeatureDetailHero` | H1, subtext, 2 CTAs, phone mockup |
| 3 | **Answer block** | `AnswerBlock` | 40 to 90 words. §6.1 |
| 4 | Stats band | `FeatureCardGrid` | 4 numbers, each sourced. Omit if unsourced |
| 5 | Workflows grid | `FeatureCardGrid` 3-col | 6 cards |
| 6 | Alternating rows | `AlternatingRow` | 2 or 3, industry-specific |
| 7 | Sub-industry grid | `SubIndustryGrid` | From the taxonomy. All panels in DOM |
| 8 | **Template library** | `TemplateLibrary` | 6 to 10 copyable messages. §7 |
| 9 | **Comparison table** | `ComparisonTable` | One per page. §6.3 |
| 10 | Four-step setup | `FeatureCardGrid` 4-col | Same 4 steps sitewide |
| 11 | FAQ | `Faq` | 6 to 8 questions |
| 12 | Related industries | generated | 3 siblings |
| 13 | CTA band | `CtaBlock` | Existing, unchanged |

Tier 2 pages run blocks 1, 2, 3, 5, 7, 8, 11, 12, 13 only.

### 4.2 Optional modules

Declared as booleans on each industry's data module.

| Module | Industries | Why |
|---|---|---|
| `integrations` | E-commerce, Retail, Logistics | Only where a real source system exists. Use §2.1 wording |
| `compliance` | Healthcare, Insurance & Finance | DPDP Act, patient data, Meta template categories. The real objection |
| `profileDescriptions` | Education, Hotels, Restaurants, Real Estate, Finance, Healthcare | Targets `whatsapp business description for <industry>` |
| `testimonials` | Only where a real named customer exists | §2.3 |
| `platformSubBlock` | E-commerce | Shopify. About 80/mo, beats 317 of 321 industries |
| `roiCalculator` | E-commerce, Retail | Dwell time and sales value |
| `seasonalTemplates` | Retail, Restaurants, Jewellery, Gyms | Festival and New Year demand is real and spiky |

### 4.3 The sub-industry grid, build note

The mockup builds this as tabs. **Render every panel into the HTML and hide with
CSS, or use native `<details>`.** JavaScript that swaps `innerHTML` leaves the
content invisible to crawlers and to AI extractors, and this block is the whole
L2 taxonomy. Same rule applies to the FAQ accordion.

---

## 5. Content rules

### 5.1 Uniqueness

Per the programmatic SEO quality gates:

| Metric | Threshold | Action |
|---|---|---|
| Unique body copy between any two pages | below 40% | flag, rewrite |
| Unique body copy between any two pages | below 30% | **hard stop, do not publish** |
| Word count, Tier 1 | below 700 | rewrite |
| Word count, Tier 2 | below 400 | rewrite |
| Human review | 100% of pages | owner signs off before publish |

Shared header, footer and nav are excluded from the calculation. Template
boilerplate **is** included.

At 18 pages the set sits well under the skill's warning gate of 100 pages and its
hard stop at 500. Progressive rollout at 2 pages per batch is already compliant.

### 5.2 The standalone value test

Every page must pass: **"Would this page be worth publishing even if no other
industry page existed?"**

If the answer is no, the page is a mad-lib and does not ship.

### 5.3 Banned

- Any sentence that works after swapping the industry noun. Rewrite it.
- Invented statistics, customers, logos, or capabilities.
- Any product claim not already true on an existing feature page.
- Em-dash, anywhere. CLAUDE.md §14.
- Fabricated Waplify dashboard screenshots. CLAUDE.md §15.3.

### 5.4 Required per page

- At least eight sentences that only work for this industry.
- The industry's own vocabulary. Coaching institutes say "batch" and "fee
  reminder". Real estate says "site visit" and "channel partner". This is the
  entire difference between 18 pages and one page printed 18 times.
- One honest objection, answered rather than dodged.

---

## 6. AEO and GEO requirements

### 6.1 Answer block, mandatory

Directly under the hero. An H2 phrased as the question, then one self-contained
paragraph of 40 to 90 words. No pronouns referring to earlier text.

Template:

> **How do `<industry>` businesses use the WhatsApp Business API?**
> `<One paragraph naming 3 to 4 concrete uses in this industry's own words, then
> one sentence on why WhatsApp beats the alternative they use today, then one
> sentence on what setup involves.>`

This is the highest-leverage AEO element on the page. AI engines lift passages
that answer completely without surrounding context.

### 6.2 FAQ answers

40 to 90 words each. Every answer must read correctly if quoted alone. Emits
`FAQPage` schema. Six to eight per Tier 1 page.

### 6.3 Comparison table, mandatory

One table per page. AI engines lift tables readily. Suggested shape:

| | WhatsApp with Waplify | SMS | Email | Phone calls |
|---|---|---|---|---|
| `<industry-specific row>` | | | | |

Rows must be industry-specific, not generic. For gyms: renewal reminder response
rate. For clinics: no-show reduction. For e-commerce: cart recovery.

### 6.4 Freshness and trust signals

- Visible "Last updated: `<date>`" on the page.
- `dateModified` in schema matching it.
- A named reviewer with a role, for example "Reviewed by `<name>`, WhatsApp API
  specialist at Waplify". Real person, real role.
- `pageDates.ts` entry with the true publish date. Never backdate, never refresh
  to look current.

### 6.5 Crawlability

- All tab and accordion content in the DOM.
- Self-referencing absolute canonical.
- Internal links as real `<a href>`, never JavaScript click handlers.
- Registry flows into `llms.txt` automatically. Verify after each publish.

---

## 7. The template library block

The block that carries the actual measured demand.

**Per page:** 6 to 10 real messages, each in a copy block with a copy button, each
labelled with its Meta template category (Marketing, Utility, or Authentication),
because buyers genuinely ask which category a message falls into.

**Industry-neutral keywords this block can also serve**, reusable across pages
with 2 to 3 industry-specific templates swapped in:

| Keyword | Vol |
|---|---|
| business message for customers | 12,100 |
| message to customers | 3,600 |
| thank you message for customers | 1,600 |
| whatsapp marketing message template | 1,600 |
| whatsapp message template | 1,300 |
| payment confirmation message | 720 |
| birthday wishes for customers | 720 |
| greeting messages for whatsapp business | 480 |
| order confirmation message | 390 |
| diwali wishes for customers | 320 |
| away message for whatsapp business | 170 |

**Promotion rule:** if a page earns impressions for its template keyword, promote
that template to its own page at `/whatsapp-templates/<use-case>/`. If not, the
cost was a section, not a page.

---

## 8. Per-industry build sheets

Volumes are India, monthly, measured. Sub-industries come from
[`PLAN-A7-INDUSTRY-PAGES.md`](PLAN-A7-INDUSTRY-PAGES.md).

### TIER 1

---

#### 1. Education & Coaching

| Field | Value |
|---|---|
| Slug | `/industries/education/` |
| H1 | WhatsApp Business API for Schools and Coaching Institutes |
| Meta title | WhatsApp for Schools and Coaching Institutes \| Waplify |
| Meta description | Send fee reminders, exam results and parent updates on WhatsApp. Admission enquiry chatbot, batch broadcasts and profile description examples for schools and coaching institutes. |
| Page volume | 5,970/mo |
| Modules | `profileDescriptions` (lead with it), `compliance` (light) |

**Keywords:** whatsapp business description for education 4,400 · whatsapp business
for education 390 · business whatsapp description for education 170 · whatsapp for
schools 170 · whatsapp for education 140 · parent teacher meeting message 110 ·
whatsapp business description for education sample 50 · fee reminder message to
parents 40 · whatsapp chatbot for education 20

**Alternating rows:** admission enquiry chatbot · fee reminders and receipts ·
parent communication and results

**Sub-industries:** Schools K-12, Colleges & universities, Coaching institutes,
EdTech platforms, Tutoring & home tuition, Preschools & daycare, Vocational
training, Language schools, Study abroad consultants, Competitive exam prep,
Coding bootcamps, Professional certification

**Templates:** fee reminder to parents · parent-teacher meeting notice · exam
result notification · admission enquiry reply · fee receipt confirmation · holiday
and schedule notice · batch start reminder · demo class invite

**Special section:** "WhatsApp Business profile description examples for schools
and institutes". Eight to ten copyable descriptions. This single keyword is 4,400
and the intent is copy-paste, so give them the text.

**Warning:** the 4,400 keyword is down 84% year on year (8,100 in Sep 2025, 390 in
Jun 2026). Build it, do not model it as durable traffic.

**Link to:** `top-whatsapp-platforms-education`, `/whatsapp-broadcast/`,
`/whatsapp-autoresponder/`, `/whatsapp-contact-management-software/`

---

#### 2. Insurance & Finance

| Field | Value |
|---|---|
| Slug | `/industries/insurance-and-finance/` |
| H1 | WhatsApp Business API for Insurance Agents and Loan DSAs |
| Meta title | WhatsApp for Insurance Agents and Loan DSAs \| Waplify |
| Meta description | Policy renewal reminders, premium due alerts, KYC document collection and claim status updates on WhatsApp. Built for insurance agents, loan DSAs and financial advisors in India. |
| Page volume | 3,080/mo |
| Modules | `compliance` (Meta template categories), `profileDescriptions` |

**Keywords:** payment reminder message 1,900 · insurance renewal message 880 ·
financial year-end message to customers 70 · financial year end thank you message
to customers 50 · whatsapp business description for finance 50 · whatsapp chatbot
for insurance 10 · whatsapp chatbot for banking 10 · whatsapp api for banking 10

**Alternating rows:** renewal reminder sequences · KYC document collection ·
claim and application status updates

**Sub-industries:** Insurance agents & brokers, Loan DSAs & brokers, Mutual fund
distributors, Stock brokers, Tax consultants & CAs, Financial planners, NBFCs,
Payment solution providers, Chit funds

**Templates:** premium renewal reminder · policy issued confirmation · claim
status update · EMI due reminder · KYC document request · financial year-end thank
you · SIP reminder · maturity notification

**Do NOT target**, SERP-verified recipient intent, the searcher is a borrower not
a lender: `loan approval message` 40,500 · `emi due message` 8,100 ·
`account balance message` 1,300

**Compliance module must cover:** which messages are Utility vs Marketing under
Meta's categories, and why that changes cost and opt-in rules.

**Link to:** `whatsapp-insurance-2026-checklist-waplify`,
`waplify-crm-whatsapp-lead-management`, `/whatsapp-autoresponder/`,
`/whatsapp-message-templates/`

---

#### 3. Gyms & Fitness

| Field | Value |
|---|---|
| Slug | `/industries/gyms/` |
| H1 | WhatsApp for Gyms and Fitness Studios |
| Meta title | WhatsApp for Gyms and Fitness Studios \| Waplify |
| Meta description | Membership renewal reminders, class schedules and win-back campaigns on WhatsApp. Copy-paste message templates for gyms, yoga studios and personal trainers. |
| Page volume | 320/mo |
| Modules | `seasonalTemplates` (New Year), `testimonials` if a real gym exists |

**Keywords:** gym membership renewal message 320

**Quality note:** one keyword, but the best in the entire taxonomy. Live SERP check
returned 100% business-facing template resources: DelightChat at position 1, plus
Textmagic, Clerk Chat, Textdrip, GymNudge and Moviq. An AI Overview cites four
small vendors, which is an open citation slot.

**Alternating rows:** membership renewal on autopilot · class schedule broadcasts ·
winning back lapsed members

**Sub-industries:** Gyms & fitness centres, Yoga studios, Personal trainers,
Sports academies, Martial arts studios, Dance fitness, Nutrition & dietitians,
Weight loss clinics, Meditation centres, Pilates & barre studios

**Templates:** membership renewal reminder (7 day, 1 day) · class schedule
broadcast · PT session confirmation · diet plan delivery · lapsed member win-back ·
payment due · New Year offer · attendance streak congratulation

**Link to:** `/whatsapp-autoresponder/`, `/whatsapp-broadcast/`,
`/whatsapp-contact-management-software/`, `whatsapp-drip-sequence-guide-2026`

---

#### 4. E-commerce & D2C

| Field | Value |
|---|---|
| Slug | `/industries/ecommerce/` |
| H1 | WhatsApp Business API for E-commerce and D2C Brands |
| Meta title | WhatsApp Business API for Ecommerce and D2C Brands \| Waplify |
| Meta description | Recover abandoned carts, confirm COD orders before dispatch and send order updates on WhatsApp. Connect Shopify or WooCommerce by webhook. Built for Indian D2C brands. |
| Page volume | 300/mo |
| Modules | `integrations`, `platformSubBlock` (Shopify), `roiCalculator`, `testimonials` if real |

**Keywords:** whatsapp chatbot for ecommerce 40 · whatsapp for business catalogue
40 · whatsapp for ecommerce 40 · whatsapp business api for ecommerce 30 · whatsapp
api for ecommerce 20 · whatsapp api for shopify 20 · whatsapp marketing shopify 20 ·
whatsapp marketing for ecommerce 20 · whatsapp automation for shopify 20 ·
whatsapp api for woocommerce 10 · whatsapp marketing for d2c brands 10

**Alternating rows:** abandoned cart recovery · COD confirmation and RTO reduction ·
order tracking and re-orders

**Sub-industries:** Online stores & D2C, Marketplace sellers, Dropshipping,
Subscription boxes, Fashion & apparel, Beauty & cosmetics, Electronics & gadgets,
Grocery & supermarkets, Furniture & home decor, Health & supplements, Pet
supplies, Sports & outdoor gear, Luxury & premium, Second-hand & resale, Gifting
& hampers

**Templates:** order confirmation · COD verification · out for delivery · delivered
with feedback request · abandoned cart recovery · back in stock · return and refund
status · repeat purchase nudge

**Shopify sub-block:** Shopify keywords total about 80/mo, beating 317 of the 321
industries tested. Platform names outperform industry names. Candidate for
`/industries/ecommerce/shopify/` if the block earns impressions.

**Link to:** `whatsapp-store-guide-2026-grow-sales`,
`whatsapp-catalog-optimization-strategies`, `/whatsapp-automation/`,
`/whatsapp-broadcast/`, `/integrations/`

---

#### 5. Retail & Local Shops

| Field | Value |
|---|---|
| Slug | `/industries/retail/` |
| H1 | WhatsApp for Retail Shops and Local Stores |
| Meta title | WhatsApp for Retail Shops and Local Stores \| Waplify |
| Meta description | Shop opening announcements, new arrival broadcasts and festival offers on WhatsApp. Ready-to-use message templates for retail shops, kirana stores and showrooms. |
| Page volume | 430/mo |
| Modules | `seasonalTemplates`, `roiCalculator`, `integrations` |

**Keywords:** shop opening message 170 · shop shifting message to customers 90 ·
shop anniversary message to customers 70 · new shop opening message to customers 50 ·
shop opening message to customers 20 · shop shifting message to customers in hindi 20 ·
whatsapp business for grocery store 10

**Build note:** every keyword here is template-shaped. **Lead with the template
library**, do not bury it. Include a Hindi variant, which has its own measured demand.

**Alternating rows:** new arrival broadcasts · loyalty and repeat visits · festival
campaigns

**Sub-industries:** Fashion & apparel stores, Jewellery stores, Electronics &
appliance stores, Grocery & kirana stores, Furniture & home furnishing, Pharmacy &
medical stores, Optical stores, Footwear stores, Watch & accessories, Hardware &
paint, Mobile repair, Florists

**Templates:** new shop opening announcement · shop shifting notice · shop
anniversary offer · new arrival broadcast · festival offer · loyalty reward ·
stock arrival alert · closing sale

**Link to:** `/whatsapp-broadcast/`, `/whatsapp-contact-management-software/`,
`grocery-shopping-on-whatsapp-2026-waplify`

---

#### 6. Healthcare & Clinics

| Field | Value |
|---|---|
| Slug | `/industries/healthcare/` |
| H1 | WhatsApp Business API for Clinics and Hospitals |
| Meta title | WhatsApp for Clinics and Hospitals \| Waplify |
| Meta description | Appointment reminders, lab report delivery and prescription follow-ups on WhatsApp. Built for clinics, hospitals and diagnostic labs, with patient data handled correctly. |
| Page volume | 190/mo |
| Modules | `compliance` (DPDP, patient data), `profileDescriptions` |

**Keywords:** whatsapp chatbot for healthcare 50 · whatsapp business description
for medical health 50 · appointment confirmation message 50 · whatsapp for
healthcare 30 · appointment reminder message 20 · whatsapp for doctors 20 ·
whatsapp business for healthcare 10 · whatsapp api for healthcare 10

**Alternating rows:** appointment reminders and no-show reduction · lab report
delivery · prescription and follow-up care

**Sub-industries:** Hospitals, General clinics, Dental clinics, Eye clinics,
Dermatology, Pediatric clinics, Gynecology, IVF & fertility, Diagnostic labs,
Pharmacies, Physiotherapy centres, Mental health & counselling, Home healthcare,
Veterinary clinics

**Templates:** appointment confirmation · reminder 24 hours before · reschedule
offer · lab report ready · prescription follow-up · vaccination due · post-surgery
check-in · health checkup package

**Compliance module must cover:** patient data under the DPDP Act, what Waplify
stores, opt-in requirements, and why a health message is Utility not Marketing.
This is the objection that stops clinics buying. Answer it.

**Link to:** `whatsapp-business-api-compliance-2025-checklist`,
`24-7-whatsapp-chatbot-support`, `/whatsapp-autoresponder/`, `/whatsapp-forms/`

---

#### 7. Hotels & Travel

| Field | Value |
|---|---|
| Slug | `/industries/hotels-and-travel/` |
| H1 | WhatsApp Business API for Hotels and Travel Agencies |
| Meta title | WhatsApp for Hotels and Travel Agencies \| Waplify |
| Meta description | Booking confirmations, check-in instructions, itinerary sharing and review requests on WhatsApp. Built for hotels, resorts, travel agencies and tour operators. |
| Page volume | 300/mo |
| Modules | `profileDescriptions`, `testimonials` if real |

**Keywords:** whatsapp for hotels 140 · whatsapp business description for hotel 110 ·
whatsapp business description for travel agency 30 · whatsapp chatbot for travel
agency 10 · whatsapp business for hotels 10

**Commercial note:** `whatsapp for hotels` carries a top-of-page bid of 6.94, the
highest commercial signal of any industry keyword found. Advertisers pay real money
for this audience.

**Alternating rows:** booking confirmation and pre-arrival · itinerary and document
sharing · reviews and repeat bookings

**Sub-industries:** Hotels & resorts, Travel agencies, Tour operators, Homestays &
B&Bs, OTAs, Visa agencies, Adventure tourism, Pilgrimage tourism, Destination
management, Budget & backpacker

**Templates:** booking confirmation · check-in instructions · pre-arrival upsell ·
checkout and review request · itinerary share · visa document checklist · flight or
schedule change · seasonal package offer

**Link to:** `whatsapp-api-vs-3rd-party-travel-confirmations`,
`/whatsapp-message-templates/`, `/whatsapp-team-inbox/`

---

#### 8. Real Estate

| Field | Value |
|---|---|
| Slug | `/industries/real-estate/` |
| H1 | WhatsApp Business API for Real Estate Agents and Builders |
| Meta title | WhatsApp for Real Estate Agents and Builders \| Waplify |
| Meta description | Property listing broadcasts, site visit scheduling and follow-up sequences on WhatsApp. Built for brokers, builders and channel partners in India. |
| Page volume | 130/mo |
| Modules | `profileDescriptions`, `integrations` (CRM webhook) |

**Keywords:** whatsapp business for real estate 40 · whatsapp marketing for real
estate 40 · whatsapp business description for real estate 30 · real estate whatsapp
marketing 10

**Alternating rows:** listing broadcasts to segmented buyers · site visit
scheduling and reminders · follow-up sequences that do not stop at one message

**Sub-industries:** Residential brokers & agents, Commercial real estate, Property
developers & builders, Rental agencies, Co-working spaces, Property management,
Interior designers, Architects, Home loan agents, Construction materials suppliers

**Templates:** new listing broadcast · site visit confirmation · site visit
reminder · price and EMI breakdown · document checklist · post-visit follow-up ·
booking confirmation · possession update

**Best existing blog support of any industry on this list. Link to:**
`whatsapp-real-estate-lead-guide`, `waplify-crm-whatsapp-lead-management`,
`b2b-whatsapp-lead-gen-framework`, `/whatsapp-crm-integration/`, `/whatsapp-forms/`

---

#### 9. Restaurants & Food

| Field | Value |
|---|---|
| Slug | `/industries/restaurants/` |
| H1 | WhatsApp for Restaurants and Cloud Kitchens |
| Meta title | WhatsApp for Restaurants and Cloud Kitchens \| Waplify |
| Meta description | Share daily specials, confirm orders and collect feedback on WhatsApp. Message templates for restaurants, cafes, cloud kitchens and catering businesses. |
| Page volume | 120/mo |
| Modules | `seasonalTemplates`, `profileDescriptions` |

**Keywords:** whatsapp business description for restaurant 50 · restaurant message
to customers 30 · whatsapp marketing for restaurants 20 · whatsapp business for
restaurants 20 · whatsapp for restaurants 10

**Alternating rows:** menu and daily specials · order and delivery updates ·
feedback and repeat orders

**Sub-industries:** Restaurants, Cloud kitchens, Cafes & coffee shops, QSR & fast
food, Bakeries & confectioneries, Sweet shops, Ice cream parlours, Catering
services, Tiffin & meal prep, Food delivery businesses, Breweries & bars, Packaged
food brands

**Templates:** daily specials broadcast · order confirmation · table reservation
confirmation · out for delivery · feedback request · festival menu · loyalty offer ·
event catering enquiry reply

**Link to:** `whatsapp-catalog-optimization-strategies`,
`example-whatsapp-broadcast-message`, `/whatsapp-broadcast/`,
`/whatsapp-chatbot-builder-for-indian-businesses/`

---

#### 10. Courier & Delivery

| Field | Value |
|---|---|
| Slug | `/industries/logistics/` |
| H1 | WhatsApp for Courier and Delivery Businesses |
| Meta title | WhatsApp for Courier and Delivery Businesses \| Waplify |
| Meta description | Shipment tracking updates, delivery attempt alerts and proof of delivery on WhatsApp. Built for courier companies, last-mile fleets and packers and movers. |
| Page volume | 110/mo |
| Modules | `integrations` (API-led) |

**Keywords:** delivery message to customer 110

**Alternating rows:** automated tracking updates · failed delivery and reschedule ·
proof of delivery

**Sub-industries:** Courier & delivery services, Freight & cargo, Warehousing &
storage, Last-mile delivery, Packers & movers, Customs & clearing agents, Cold
chain logistics, Trucking & fleet operators

**Templates:** shipment picked up · out for delivery · delivery attempted ·
delivered with POD · reschedule request · address confirmation · pickup scheduled ·
rate enquiry reply

**Link to:** `whatsapp-webhooks-no-code-setup`,
`whatsapp-business-api-integrations-2026-automation`, `/waplify-api/`,
`/whatsapp-automation/`

---

### TIER 2

Spine only, minus the stats band. 400 to 500 words. Navbar and sales assets. All
measured at zero volume, which is fine and expected.

| Industry | Slug | H1 | Row topics |
|---|---|---|---|
| Beauty & Salons | `/industries/salons/` | WhatsApp for Salons and Spas | booking, reminders, offers |
| Weddings & Events | `/industries/events/` | WhatsApp for Wedding Planners and Event Companies | RSVP, vendor coordination, schedules |
| Automotive | `/industries/automotive/` | WhatsApp for Car Dealers and Service Centres | test drive, service reminders, insurance renewal |
| Home Services | `/industries/home-services/` | WhatsApp for Home Service Businesses | booking, technician dispatch, feedback |
| Professional Services | `/industries/professional-services/` | WhatsApp for CA Firms and Law Firms | lead qualification, document collection, status updates |
| Manufacturing | `/industries/manufacturing/` | WhatsApp for Manufacturers and Distributors | dealer broadcasts, dispatch alerts, order status |
| Agriculture | `/industries/agriculture/` | WhatsApp for Agri Businesses | price alerts, advisories, input orders |
| Jewellery & Boutiques | `/industries/jewellery-and-boutiques/` | WhatsApp for Jewellery Stores and Boutiques | catalogue sharing, new collection, festival offers |

Beauty & Personal Care measured **zero across all 52 terms and every pattern**.
Its page is purely a sales asset. Budget writing time accordingly.

---

## 9. Schema specification

Every Tier 1 page emits five blocks. Tier 2 emits the first three.

| Schema | Applies to | Notes |
|---|---|---|
| `BreadcrumbList` | all | Home > Industries > this page |
| `FAQPage` | all with an FAQ | Question and acceptedAnswer per item |
| `SoftwareApplication` | all | Reuse the existing sitewide block |
| `ItemList` | Tier 1 | Wrap the sub-industry grid |
| `HowTo` | Tier 1 | Wrap the four-step setup |

Also required on every page:

- Self-referencing absolute `<link rel="canonical">`
- `datePublished` and `dateModified` from `pageDates.ts`
- Visible last-updated date matching `dateModified`

---

## 10. Content production pipeline

Per page, in order:

1. **Owner brief.** Six inputs: who exactly this is for, a real customer or
   "none yet", four to six real use cases, the objection that stops them buying,
   this industry's vocabulary, and the ranked features that matter here.
2. **Draft** generated against the brief and this file.
3. **Owner review.** The release gate. See §12 checklist.
4. **Developer build.** Data module, registry entry, `pageDates.ts`.
5. **QA gate.** §12.
6. **Publish.** Two pages per batch.

---

## 11. Internal linking

### Inbound, all automatic from the registry

Header megamenu (Tier 1), footer column, hub grid, three sibling strips, plus
feature-page strips added in the consolidation batch.

**Floor: four inbound internal links per page.** Checked at audit.

### Outbound, enforced at review

| Target | Count |
|---|---|
| Feature pages, at least 2 in body prose not just cards | 4 to 5 |
| `/pricing/` | 1 |
| `/integrations/` or `/whatsapp-crm-integration/` where a real source system exists | 1 |
| Blog posts, named per industry in §8 | 1 to 2 |
| Sibling industries | 3, generated |
| `/industries/` hub via breadcrumb | 1 |

**Density target:** 3 to 5 internal links per 1,000 words. Varied descriptive
anchor text, never repeated exact-match keywords.

---

## 12. QA gate

Nothing publishes until every box is ticked.

### Build
- [ ] `npx astro check`: 0 errors, 0 warnings, 0 hints
- [ ] Registry entry added
- [ ] `pageDates.ts` entry with the real date
- [ ] Route renders, no console errors

### Content
- [ ] Word count in range (Tier 1: 700 to 900. Tier 2: 400 to 500)
- [ ] 40% or more unique body copy versus every other industry page
- [ ] Passes the standalone value test, §5.2
- [ ] Eight or more sentences that only work for this industry
- [ ] Every statistic sourced on-page, or removed
- [ ] Every named customer real and consenting, or block removed
- [ ] No capability claim absent from an existing feature page
- [ ] `grep -rn "$(printf '\xe2\x80\x94')" site/src` finds nothing new

### AEO
- [ ] Answer block present, 40 to 90 words, self-contained
- [ ] FAQ answers 40 to 90 words, each quotable alone
- [ ] Comparison table present with industry-specific rows
- [ ] Visible last-updated date
- [ ] Named reviewer with role
- [ ] Sub-industry panels and FAQ answers present in the DOM, not JS-injected

### Schema
- [ ] BreadcrumbList, FAQPage, SoftwareApplication valid
- [ ] ItemList on sub-industry grid (Tier 1)
- [ ] HowTo on the four steps (Tier 1)
- [ ] Canonical self-referencing and absolute

### Links
- [ ] Outbound minimums met per §11
- [ ] Related industries strip renders 3 siblings
- [ ] Every internal link returns 200, no redirect chains

### Post-deploy on production
- [ ] URL returns 200
- [ ] Present in `sitemap-index.xml` and `llms.txt`
- [ ] Visible in megamenu (Tier 1) and footer
- [ ] Appears on `/industries/` hub
- [ ] Tracker row updated, §15

---

## 13. Open decisions, owner input needed

These block the first publish, not the build.

### 13.1 URL shape

Default in this spec is `/industries/<industry>/`. The owner prefers
`/whatsapp-for-<industry>/`.

The original SEO reason for flat root is gone: it existed to place a target
keyword near the root, and measurement found no target keyword. So decide on
other grounds.

| | `/whatsapp-for-ecommerce/` | `/industries/ecommerce/` |
|---|---|---|
| Reads to a human | slightly better | fine |
| Breadcrumb | needs a virtual parent | natural |
| Route files | 18 thin files, root `[slug].astro` is taken by the blog | one dynamic route |
| Sub-industry children later | reads oddly | clean |

**Recommendation: `/industries/`.** The deciding factor is the mockup's own
sub-industry section, which implies L2 pages are coming and they need a parent path.

Flat root remains a legitimate choice. Cost is 18 route files and awkward L2 URLs.
**Slugs freeze on publish.**

### 13.2 Statistics

Are the four hero stats real Waplify numbers, industry figures, or neither?
Determines whether the stats band ships. §2.2.

### 13.3 Testimonials

Which industries have a real, named, consenting customer? Determines where the
testimonial block ships. §2.3.

---

## 14. Handoff prompt for any AI session

```
I am building 18 industry landing pages for waplify.io, a WhatsApp Business API
platform for Indian businesses.

READ FIRST, IN THIS ORDER:
1. docs/PLAN-INDUSTRY-PAGES-MASTER.md   the build spec, this is authoritative
2. docs/A7-KEYWORDS-WITH-VOLUME.md      the measured keyword data
3. CLAUDE.md and docs/DECISIONS.md      the repo rules

Then:
1. Read sections 1 and 2 of the master spec. Those decisions are CLOSED.
2. Read section 15 to find what is live and what is next.
3. Tell me which batch we are on and what today's tasks are.
4. Do only today's tasks. Two pages per batch, three days per batch.
5. Update section 15 and section 16 in the same commit as any work.

THE MOST IMPORTANT THING TO UNDERSTAND:
These pages are NOT an SEO play. Measurement across 1,585 taxonomy terms found
roughly 750 monthly searches for the entire industry family, and 314 of 321
industries return zero. The pages exist for navigation, sales, and to test the
message-template family cheaply. If you find yourself proposing more industry
pages "for SEO", or padding a page to rank, stop.

HARD RULES:
- Never invent a statistic, a customer, a logo, or a capability.
- Waplify ships TWO connectors: IndiaMART and Google Sheets, plus REST API and
  webhooks. Never imply more.
- No em-dash anywhere. CLAUDE.md section 14.
- 700 to 900 words Tier 1, 400 to 500 Tier 2. Do not pad.
- 40% minimum unique copy between any two pages. Below 30% is a hard stop.
- Every page passes the full QA gate in section 12 before publish.
- Slugs freeze on publish.

Current position: <fill in, for example "Batch 2, building Gyms and E-commerce">
```

---

## 15. Progress tracker

Status values: `PLANNED`, `BRIEFED`, `DRAFTED`, `BUILT`, `LIVE`.

| # | Tier | Page | Slug | Status | Published | Words | Unique % | Links in |
|---|---|---|---|---|---|---|---|---|
| 0 | - | Hub | `/industries/` | LIVE | 2026-08-24 | 450 | 100% | 5 |
| 1 | 1 | Education & Coaching | `/industries/education/` | LIVE | 2026-08-24 | 850 | 100% | 6 |
| 2 | 1 | Insurance & Finance | `/industries/insurance-and-finance/` | PLANNED | | | | |
| 3 | 1 | Gyms & Fitness | `/industries/gyms/` | LIVE | 2026-08-26 | 820 | 100% | 6 |
| 4 | 1 | E-commerce & D2C | `/industries/ecommerce/` | PLANNED | | | | |
| 5 | 1 | Retail & Local Shops | `/industries/retail/` | PLANNED | | | | |
| 6 | 1 | Healthcare & Clinics | `/industries/healthcare/` | PLANNED | | | | |
| 7 | 1 | Hotels & Travel | `/industries/hotels-and-travel/` | PLANNED | | | | |
| 8 | 1 | Real Estate | `/industries/real-estate/` | PLANNED | | | | |
| 9 | 1 | Restaurants & Food | `/industries/restaurants/` | PLANNED | | | | |
| 10 | 1 | Courier & Delivery | `/industries/logistics/` | PLANNED | | | | |
| 11 | 2 | Beauty & Salons | `/industries/salons/` | PLANNED | | | | |
| 12 | 2 | Weddings & Events | `/industries/events/` | PLANNED | | | | |
| 13 | 2 | Automotive | `/industries/automotive/` | PLANNED | | | | |
| 14 | 2 | Home Services | `/industries/home-services/` | PLANNED | | | | |
| 15 | 2 | Professional Services | `/industries/professional-services/` | PLANNED | | | | |
| 16 | 2 | Manufacturing | `/industries/manufacturing/` | PLANNED | | | | |
| 17 | 2 | Agriculture | `/industries/agriculture/` | PLANNED | | | | |
| 18 | 2 | Jewellery & Boutiques | `/industries/jewellery-and-boutiques/` | PLANNED | | | | |

### Build order

| Batch | Pages | Day |
|---|---|---|
| Foundation | types, registry, template, new components, hub, nav | 1 to 4 |
| 1 | Education, Insurance & Finance | 5 |
| 2 | Gyms, E-commerce | 8 |
| 3 | Retail, Healthcare | 11 |
| 4 | Hotels & Travel, Real Estate | 14 |
| 5 | Restaurants, Logistics | 17 |
| 6 | Salons, Events | 20 |
| 7 | Automotive, Home Services | 23 |
| 8 | Professional Services, Manufacturing | 26 |
| 9 | Agriculture, Jewellery & Boutiques | 29 |
| Audit | reverse links from feature pages, blog links, schema sweep, baseline | 30 |

**Build E-commerce first as the reference page**, even though it is in batch 2,
then clone its structure. It uses the most modules.

---

## 16. Change log

| Date | Change | Reason |
|---|---|---|
| 2026-09-10 | Built sub-industry page: EdTech Platforms (`/industries/education/edtech-platforms/`) on industry branch | User request |
| 2026-09-08 | Built & published sub-industry page: Coaching Institutes (`/industries/education/coaching-institutes/`) | User request |
| 2026-09-08 | Built & published sub-industry page: Colleges & Universities (`/industries/education/colleges-and-universities/`) | User request |
| 2026-08-21 | Master spec created, consolidating four planning documents | Owner asked for one file to build from |
| 2026-08-21 | Integration logo strip replaced with honest connector/webhook labels | Waplify ships two connectors. CLAUDE.md §15.3 rule 3 |
| 2026-08-21 | Answer block, comparison table, template library added to the spine | AEO. AI engines lift self-contained passages and tables |
| 2026-08-21 | Word budget raised to 700 to 900 for Tier 1 | Programmatic SEO quality gate: below 300 is thin, and the added AEO blocks need room |
| 2026-08-21 | Uniqueness floor set at 40%, hard stop 30% | Google Scaled Content Abuse enforcement, per the seo-programmatic skill |

---

## 17. Reality check, kept deliberately

| | Monthly searches |
|---|---|
| All 10 Tier 1 industry pages | ~10,600 |
| All 8 Tier 2 industry pages | 0 |
| One existing page, `/whatsapp-report-sample-2026/`, currently rank 34 | 27,000 |
| One keyword, `whatsapp business web` | 135,000 |

The industry pages are worth building for the navbar, for sales, and for the
template sections that test the next content family cheaply. The traffic work in
[`PLAN-SEO-PRIORITY.md`](PLAN-SEO-PRIORITY.md) is worth roughly thirty times more
and should not be displaced by this.
