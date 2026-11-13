# AISLELOOP — Concept v2 (audited and enhanced)
### Profit intelligence for supermarkets, built on the billing data and cameras they already own

**Industry:** RetailTech | Retail Analytics | Edge AI | B2B SaaS
**Version:** 2.0, 13 November 2026. Supersedes `# AISLELOOP.txt`. Each change is traced to a finding in [AISLELOOP_CLAIMS_AUDIT.md](AISLELOOP_CLAIMS_AUDIT.md), cited as [Audit X#].
**Tagline:** *Know which promotions and displays actually make you money.*

> **Evidence labels in this document:** **V** = verified against public sources; **R** = from earlier research reports, unverified hypothesis; **I** = inference; **H** = hypothesis to test in pilots. A number without a label should not appear here.

---

## 1. The problem

Supermarkets run promotions, end-caps and discounts constantly, but most cannot answer a simple question: *did this promotion make us more gross profit than it cost?*

- **POS systems record every bill but report activity, not effect.** They show what sold. They do not show what sold *because of* the promotion, net of the discount, of sales stolen from sister SKUs, and of next month's dip after customers stockpiled. (I)
- **Store cameras watch for theft, not merchandising.** They can show which zones and end-caps draw traffic and when queues build. That signal is rarely combined with sales. (I)
- **Regional chains are stuck in the middle.** They are too big to manage promotions by gut feel, and too small for enterprise analytics teams or loyalty platforms. (I/H)

**The core problem is not missing data. It is that billing data is not used to measure cause and effect, and in-store signals are never joined to it.** [Audit A3]

## 2. The solution

AisleLoop is a profit-intelligence layer that sits on top of a supermarket's existing POS and, optionally, its existing CCTV.

1. **Promotion & Margin Intelligence (core, no hardware).** Reads POS and purchase data. It **estimates** each past promotion's incremental profit against a baseline model, with an uncertainty range, and flags likely loss-makers. It also tracks supplier-scheme claims (H: value to be shown in Phase 0).
2. **Store Zone Analytics (optional add-on).** A local edge box reads selected existing cameras and outputs **only aggregate, non-identifying metrics**: zone traffic, dwell, end-cap engagement on qualified cameras, and queue length. [Audit B2, B4]
3. **Action & Test Engine.** Turns findings into a short weekly list of actions, such as stopping a promotion, moving a display or re-pricing. It then runs each action as a **controlled test** and reports the measured profit effect.
4. **Opt-in Customer Offers (later phase).** Personalised offers come **only** from POS purchase history and explicit QR/WhatsApp opt-in, **never from CCTV**. [Audit B5]

**Hard architectural rule:** camera data never links to a customer identity. It leaves the store as aggregates only. [Audit B5]

## 3. How it works

**Step 1 — Connect billing data (week 1).** A read-only connector or scheduled export takes POS line items, promotions, purchase costs and supplier schemes. Target first: POS systems common in Tamil Nadu (GoFrugal/Zoho, Logic ERP, Ginesys — R). A data-quality check runs before any analysis.

**Step 2 — Historical promotion audit (weeks 1–2, no hardware).** Six months of history produce a report. For each promotion it estimates incremental units and gross profit against a baseline, discount cost, cannibalisation and post-promo dip. It then ranks promotions as likely profitable, break-even or loss-making, each with a confidence band. These are **observational estimates, not causal proof**. Proof comes from the prospective tests in Step 5. **This audit is the sales opener.** [Audit C3, D1]

**Step 3 — Optional zone analytics (after a site survey).** A field survey scores each camera on type (IP vs analog), resolution, angle and network path. Only qualifying cameras are used. Streams are pulled from cameras or the PoE switch where possible, with a per-NVR stream cap. "No degradation of recording or live view" is an acceptance test. [Audit A2, B1, C2]

**Step 4 — Attention-to-Sales Gap (diagnostic).** For zones with qualified cameras, compare zone traffic and engagement against the category's POS sales in the same time window. A large gap is a **hypothesis to test**, not a lost sale. Its likely causes (stock-out, price, adjacency, thoroughfare traffic) are listed for the manager to check. [Audit B3]

**Step 5 — Act and test.** Each recommended action runs against a holdout: a comparable store, week, or category. Retailer rules (minimum margin, inventory, promo budget) constrain every recommendation. Ranking is always by **net retailer gross profit**. [Audit B7, B8]

**Step 6 — Report in rupees.** A weekly WhatsApp/email digest gives three to five actions and the measured outcome of last week's tests. A dashboard is available but not required. (R: dashboard-fatigue churn risk.)

## 4. MVP scope

| Build in MVP | Defer | Do not build |
|---|---|---|
| POS/purchase connector + data-quality check | Digital-display triggered promotions [Audit B6] | Facial recognition |
| Historical promotion incrementality audit | Automated optimisation / bandits [Audit B8] | Cross-camera person re-identification |
| Supplier-scheme claim tracker | Opt-in personalised offers via QR/WhatsApp | Linking CCTV to any customer identity |
| Holdout test designer + results report | FMCG brand-facing insight product | SKU recognition or pickup/put-back from ceiling CCTV [Audit B2] |
| Edge zone analytics on qualified cameras: traffic, dwell, end-cap engagement, queue | Shelf-gap detection on selected fixtures | Autonomous checkout |
| Weekly action digest | | |

## 5. Target market

**Ideal customer profile (first 10 customers):** regional supermarket chains with **about 5–50 stores** (H: band to be confirmed in interviews) in Chennai and Tamil Nadu that have: [Audit C1]
- centralised digital POS with exportable line-item data;
- an owner or promoter, or a finance/merchandising head, who decides on software;
- active promotion and supplier-scheme programmes;
- no in-house analytics team.

CCTV is **not** required for the core product. For the zone add-on, the store must pass the camera site survey. [Audit C2]

**Later:** single-store independents via a low-price POS-only tier or a POS-vendor bundle. Then pharmacies and speciality food chains.

## 6. Competition and positioning

| Category | Examples | What they do | AisleLoop stance |
|---|---|---|---|
| Existing-CCTV retail analytics | TangoEye (Chennai; Lenskart-owned since Oct 2023 — V), others | Footfall, conversion, queues, empty-shelf alerts on existing cameras (V) | **Do not compete on video features.** Integrate or co-exist where possible. |
| POS / ERP | GoFrugal/Zoho, Logic ERP, Ginesys (R) | Billing, inventory, basic reports | Integration and channel partner, and possibly a competitor if they bundle promo reports. |
| Loyalty / CDP | Capillary, Easyrewardz (R) | Loyalty, campaigns | Out of scope for the MVP; enterprise-priced (R). |
| Shelf-image audit | ParallelDots, Infilect (R) | Phone/shelf-camera SKU audits for brands | Complementary; a later partnership for SKU-level checks. |

**Differentiation.** The edge is *measured profit*, not *cameras*: [Audit C3]
1. **Profit measurement net of discount, cannibalisation and pull-forward.** It starts with baseline-model estimates on history. Then it gives **causal** results from prospective holdout tests. This is not a redemption count.
2. **Actions, not dashboards.** A weekly digest with a test attached to every recommendation.
3. **Hardware is optional.** The value is proven from POS data before any device is installed.

## 7. Success metrics

**North star:** *net incremental gross profit per store per month*, defined as:

> treatment-group gross margin − holdout-group gross margin (scaled) − discount/reward cost − measured cannibalisation − measured post-promotion dip − incremental operating cost

Guardrail rule: the same rupee is never attributed to two interventions. (Formula adapted from the earlier cost report — R.)

**Supporting metrics:** share of promotions classified loss-making; supplier claims recovered (₹); test win rate; CV metric error against manual-count ground truth per camera (at commissioning and monthly) [Audit D4]; onboarding days per store; pilot-to-paid conversion; logo and store retention.

## 8. Business model (all prices are H — test prices)

| Tier | Test price (H) | Scope |
|---|---|---|
| **Lite** | ₹3,500–5,000 / store / month (R) | POS-only promotion and margin audit, scheme tracker, weekly digest |
| **Core** | ₹7,500–9,500 / store / month + deployment (R) | Lite + holdout testing + chain reporting + zone analytics on qualified cameras |
| **Enterprise** | Scoped | Custom integrations, governance, dedicated support |

**Break-even bar for the customer.** In the earlier cost report's worked example (₹8,000–11,500/store/month plus ₹55k–68k setup per store, amortised over 12 months), a chain needs roughly **₹12.5k–17k of verified extra gross profit per store per month** to break even in year 1 (R arithmetic). At the lower Lite/Core test bands above, the bar is lower. Recompute it for the price actually quoted. The pilot target is **≥3× fee** (H, a commercial target). Hardware is a separate one-time line item. Its cost is to be benchmarked, not assumed. [Audit D1, C4]

## 9. Go-to-market

**Phase 0 — Problem validation (30 days).** Planning targets (H), adapted from the earlier cost report: interview 10–15 chain owners and finance/merchandising heads; collect anonymised six-month POS exports from about 5 chains; run 3–5 free historical audits. Gate: POS export rights and a passed data-quality test. [Audit C6]

**Phase 1 — Paid pilot.** Size the number of stores and weeks with a **power calculation** on the sales variance seen in the Phase 0 exports. More than one store is likely needed (I). The pilot carries a paid, creditable fee. Pre-register the success metric and minimum detectable effect. Use holdout stores, weeks or categories with a switchback design. Exclude major festival weeks (Pongal, Deepavali) or model them explicitly. Zone analytics is installed only in stores that pass the survey. [Audit D2]

**Phase 2 — Commercial validation.** Convert pilots to annual chain contracts. Publish case studies with method, control and confidence interval shown.

**Phase 3 — Channel.** Use POS-vendor dealers and CCTV integrators as resellers. Get written data-access terms with each POS vendor. [Audit C5]

### Kill / pivot gates (adapted from earlier reports — R)
- **Leakage:** measurable, recoverable promotion and scheme leakage across the audited chains is below the break-even bar in §8.
- **Data access:** POS data cannot be exported or reconciled for most target chains.
- **Willingness to pay:** pilots show ≥3× ROI but no chain converts to an annual contract.

## 10. Privacy, security and responsible AI

- **No facial recognition, no cross-camera tracking, no CCTV-to-identity linking.** [Audit B5]
- **Video is processed transiently on-site.** Only aggregate, non-identifying metrics leave the device. No frames or crops are stored by AisleLoop. Store CCTV retention stays under the store's existing policy. [Audit B4]
- **In-store notice signage** describes the analytics purpose.
- **DPDP readiness.** The DPDP Rules 2025 were notified on 13 Nov 2025, and most Data Fiduciary obligations apply from about **13 May 2027** (V). §9(3) bars tracking or behavioural monitoring of children, with penalties up to ₹200 crore under the Schedule (V; confirm against the Gazette). The product is designed for that regime now. [Audit E1]
- **Questions for Indian privacy counsel before launch:**
  1. Is transient on-device processing of shoppers' video (including minors) "processing of personal data" requiring notice or consent, or covered by a legitimate use?
  2. Do short-lived single-camera tracks used to compute dwell count as "tracking or behavioural monitoring" of children under §9(3)?
  3. Are AisleLoop and the retailer controller and processor (Data Fiduciary and Data Processor), and what must the DPA contain?
  4. What WhatsApp/SMS marketing-consent rules (TRAI) apply to opt-in offers?
- **Customer data:** opt-in loyalty and offer data is held separately from camera metrics, with purpose limitation and deletion on request.
- **Cross-store aggregation** happens only with contractual opt-in from each retailer, on anonymised and aggregated outputs. [Audit E2]

## 11. Long-term vision

Become the **profit-measurement layer for physical retail**: the system that tells a chain what each promotion, display, price change and layout change actually earned. Extensions follow once the core is proven: shelf-availability alerts, demand forecasting, layout testing, opt-in personalisation, and (with retailer consent) FMCG-funded display and promotion measurement. In that last product, recommendations stay ranked by retailer profit.

## 12. Elevator pitch

AisleLoop tells supermarket chains which promotions and displays actually make money. It reads the billing data they already have and estimates each promotion's profit after discounts, cannibalised sales and post-promo dips. It then proves or disproves each recommended change with a controlled test and reports the result in rupees. Where store cameras allow, it adds anonymous zone-traffic and queue signals without new cameras and without identifying anyone. Customers start with a free historical audit, then a paid pilot with a clear pass mark.

---

## Appendix — Claims register for this document

| Claim in v2 | Label | Basis |
|---|---|---|
| TangoEye sells existing-CCTV analytics (footfall, conversion, queues, empty shelves); acquired by Lenskart Oct 2023 | V | tangoeye.ai; YourStory, Business Standard (Oct 2023) |
| DPDP Rules notified 13 Nov 2025; most obligations from about 13 May 2027 | V | AZB & Partners, YourStory, Mondaq |
| DPDP §9(3) bars tracking or behavioural monitoring of children; up to ₹200 crore penalty | V (confirm Gazette) | dpdpa.com §9, CyberPeace, DPDP Wiki |
| Analog and IP CCTV are each roughly 40–55% of the Indian market | V (estimates disagree) | Mordor, IMARC, Counterpoint |
| Fine-grained shelf/checkout vision is hard (Grabango shut Oct 2024; Amazon pulled JWO from Fresh 2024) | V | Retail Dive, Grocery Dive, Business Standard |
| POS vendors common in TN: GoFrugal/Zoho, Logic ERP, Ginesys | R | earlier reports |
| Test prices ₹3.5k–9.5k/store/month; break-even ₹12.5k–17k uplift at the report's ₹8k–11.5k example price | R / H | `retail_profit_intelligence_competitor_cost_report.md` §8–9 |
| Dashboard-fatigue churn risk; hardware-support risk | R | deep research report §24 |
| Single-store pilot is underpowered; dwell ≠ intent; CCTV cannot link identity reliably | I | audit reasoning |
| Any rupee lift from AisleLoop; supplier-scheme recovery value; ICP band; ≥3× target; Phase 0 counts | H | must be shown in interviews and pilots |
| **The POS-first pivot itself** | R + I | It rests on the earlier reports and this audit's reasoning, not on retailer evidence. Phase 0 is meant to test it. |

**Removed from v1 because they could not be supported:**
- per-shopper "e-commerce-style" behaviour tracking;
- "Lost-Sale Radar" as a rupee estimate;
- digital-display triggering in the MVP;
- automatic outcome optimisation in the MVP;
- "infrastructure-light" and "local AI" as differentiators;
- "surveillance" framing.
