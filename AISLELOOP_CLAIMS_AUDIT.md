# AisleLoop — Concept Understanding and Claims Audit

**Audited document:** `C:\Users\roger\Downloads\# AISLELOOP.txt` (256 lines; line numbers below refer to it)
**Audit date:** 13 November 2026
**Companion file:** [AISLELOOP_CONCEPT_v2.md](AISLELOOP_CONCEPT_v2.md) (enhanced concept that applies the fixes below)

---

## 1. What the concept is (in one paragraph)

AisleLoop is a B2B SaaS layer for Indian supermarkets (Chennai/Tamil Nadu first). It pulls video from the store's existing CCTV, runs computer vision on a local edge box, and turns it into anonymous behaviour signals: aisle traffic, dwell, shelf-zone engagement. It joins those signals with POS, inventory, price and promotion data to find categories that draw attention but sell poorly ("Lost-Sale Radar"). It then recommends or triggers actions (display promos, QR coupons, opt-in offers, merchandising changes, staff alerts) and measures them against POS outcomes, with incremental retailer profit as the north-star metric. Pricing is tiered per store (Basic / Growth / Enterprise) plus deployment and hardware fees.

## 2. Overall verdict

**The direction is sound and the document is already more careful than most pitches.** It avoids facial recognition, scopes out SKU-level identification in the MVP, makes personalisation opt-in, and picks *incremental profit* rather than coupon volume as its success metric. Those choices are correct.

**The weak points are:**
1. **An internal contradiction on identity.** The concept says the events are anonymous but also promises personalised offers based on behaviour. It never says how the two connect.
2. **A differentiation claim that does not hold.** A Chennai competitor already sells existing-CCTV retail analytics.
3. **An unsupported core analytic.** "Attention but low sales" is treated as a measurable lost sale.
4. **Infrastructure assumptions narrower than stated.** These cover IP cameras, NVR capacity, camera angles and digital displays.
5. **A pilot design too small to prove lift.**

None of these kills the idea. All of them change what the MVP should be and how it should be pitched.

## 3. Evidence tiers used

| Tier | Meaning |
|---|---|
| **V — Verified** | Checked against public web sources during this audit (links in §7). |
| **R — Report-claimed** | Stated in the earlier AI-generated research reports in Downloads (`ai_supermarket_shopper_intelligence_deep_research_report.md`, `retail_profit_intelligence_competitor_cost_report.md`). Not independently verified. Treat it as a hypothesis. |
| **I — Inference** | Engineering or commercial reasoning, with no direct source. |

> **Caution on the earlier reports.** They are useful but are not ground truth.
> - **Amazon Just Walk Out:** the reports state the "700 of 1,000 sales required manual review" figure as fact. It is a 2022 figure reported by *The Information*, and Amazon disputes it. Amazon also kept Just Walk Out in smaller formats.
> - **DPDP §9(3):** the reports date the regime correctly (core obligations from about 13 May 2027). But they treat "aisle CCTV analytics is illegal child tracking" as settled. Whether transient, anonymous zone counts amount to "tracking or behavioural monitoring" of children is an open question for counsel.
> - **Source discrepancies:** Grabango's lifetime funding is reported as $71M, $73M or $93M depending on the source.
>
> The reports' CV accuracy bands (for example "75–85% reach detection") and price points have no primary benchmark or quote attached. Several cited sources are secondary blogs.

---

## 4. Claim-by-claim audit

### A. Problem framing

| # | Claim (line) | Verdict | Tier | Evidence / reasoning | Fix |
|---|---|---|---|---|---|
| A1 | E-commerce understands customer behaviour through views, carts and history; physical stores do not (12–16) | **True but over-extended** | I | Online signals are per-user and per-SKU. Ceiling CCTV can at best give **anonymous, zone-level aggregates**, so there is no physical equivalent of a "product view" or "abandoned cart" per shopper. The analogy sets an expectation the product cannot meet. | Reframe it as "e-commerce-style *funnel metrics at aisle/category level*", not per-shopper behaviour. |
| A2 | Supermarkets already have cameras recording these interactions (20) | **Partly true** | I + V | Most organised stores have CCTV. But security cameras are placed to watch entrances, tills, stockrooms and high-theft aisles, not shelf faces. They often view shelves end-on or from overhead, which hides hand–shelf interaction. Analog systems are still a large share of the overall Indian CCTV market (V, market-wide; supermarket share unknown). | Say "cameras exist; a site survey decides which ones are usable". |
| A3 | "The problem is not a lack of data" (24) | **Half true** | I | It holds for **POS data**, which is rich and under-used. For **shelf behaviour** it is false: existing CCTV often lacks the angle or resolution needed. The strongest under-used asset is actually the POS transaction log. | Lead with POS under-use; position CCTV as a supporting signal. |

### B. Solution and how it works

| # | Claim (line) | Verdict | Tier | Evidence / reasoning | Fix |
|---|---|---|---|---|---|
| B1 | Connects via standard streaming protocols "without interfering" with surveillance (42) | **Needs a condition** | R + I | RTSP and ONVIF are standard. The reports claim budget NVRs cap outgoing streams at about 20–80 Mbps (R). Pulling many extra streams from an NVR can degrade live view or recording (I). | Pull **sub-streams directly from cameras via the PoE switch** where possible. Cap streams per NVR. Make "no impact on recording" a site-survey acceptance test. |
| B2 | Detects "interactions with shelves and products, where camera positioning allows" (50) | **Honestly hedged; scope is unstated** | R + I | The report estimates bay-level reach detection is workable only on side-oblique cameras close to the shelf, and that pickup/put-back and SKU recognition are not reliable from ceiling CCTV (R; no primary benchmark). The cashierless-checkout failures show how hard fine-grained shelf vision is: Grabango shut down in Oct 2024, and Amazon removed Just Walk Out from its Fresh grocery stores in 2024 (V). | State the MVP outputs explicitly: **zone traffic, dwell, end-cap/bay engagement on qualified cameras, queue length**. No SKU or pickup claims. |
| B3 | "Products or categories receiving significant attention but relatively low sales" (51, 92 "Lost-Sale Radar") | **Overclaimed — the central weakness** | I | (1) **Dwell is not intent.** People stand in aisles because of congestion, a thoroughfare, waiting companions, searching or price checking. (2) **Join problem:** CCTV gives *zone visitors*; POS gives *category baskets*. The ratio is an aggregate correlation over a time window, not a per-shopper conversion. (3) **Confounders:** stock-outs, price changes, adjacency effects, staff traffic. A low ratio is a *hypothesis to test*, not a lost sale. | Rename to an **"Attention-to-Sales Gap" indicator**. Label it as a diagnostic, and require a controlled test before any rupee value is attributed to it. |
| B4 | Events are anonymised rather than relying on personal identification (54) | **Good design; wording legally imprecise** | V + I | Raw frames of identifiable people are personal data at capture, even when only counts leave the box. "Anonymous" applies to the **output**, not the ingestion. DPDP Rules were notified 13 Nov 2025, with most obligations phased in by about 13 May 2027 (V). | Say "processes video transiently on-site and **outputs** only aggregate, non-identifying metrics". Add notice signage, a retention limit and a purpose limit. Get counsel review. |
| B5 | Opt-in customers get personalised promotions and post-visit follow-ups (34, 70, 100, 232) | **Contradiction with B4** | I + R | The concept never says what links a person's in-store behaviour to their identity. Doing so from CCTV would require face recognition or cross-camera tracking tied to a QR scan, which contradicts B4. The report also flags it as technically unreliable and legally risky, especially given DPDP §9(3) on tracking children (R/V). | **Personalisation uses only POS basket plus explicit QR/WhatsApp opt-in. It never uses CCTV.** CCTV stays aggregate-only. State this as a hard architectural rule. |
| B6 | Contextual promotions on digital displays (68) | **Assumes infrastructure absent in the ICP** | I | Most independent and regional Indian supermarkets have little in-aisle digital signage. Behaviour-triggered display content also has unproven incremental value. | Move it to a later phase. The MVP intervention set is: shelf QR coupons, merchandising/end-cap changes, staff and restock alerts, and promo go/no-go. |
| B7 | Decision engine controlled by retailer rules (margin, inventory, budget) (74) | **Sound** | I | This is the right guardrail. | Keep it. Add a rule that recommendations are ranked by **net retailer gross profit**, including when brand funding is involved. |
| B8 | Closed loop optimises interventions over time from outcomes (82) | **Premature** | I | A few stores generate too few independent experiments for automated optimisation. Weekly category sales are noisy. Early "learning" would mostly fit noise. | Phase 1 runs **explicit A/B or holdout tests** analysed by humans. Automated optimisation comes only once there are enough stores and test history. |

### C. Market and competition

| # | Claim (line) | Verdict | Tier | Evidence / reasoning | Fix |
|---|---|---|---|---|---|
| C1 | Initial customers: independent supermarkets **and** 3–50 outlet chains (112, 118) | **Internally inconsistent** | R + I | Single-store independents have thin margins and small software budgets. The report notes POS spend in the hundreds to low thousands of ₹/month (R). Chains with 5–50 stores have central data, a reachable decision-maker and enough stores to run controlled tests. | ICP: **regional chains with roughly 5–50 stores** and central POS. Independents come later via a POS-only "Lite" tier or POS-partner bundles. |
| C2 | ICP has IP-based CCTV (116) | **Likely narrows the market; size unknown** | V (market-wide) + I | Industry estimates put analog/HD-analog at roughly 40–52% of India's *whole* CCTV market, with IP at about 41–55% and growing (V). No supermarket-specific split was found, and analog share is said to be strongest in public-sector tenders, so the supermarket share is unknown (I). Analog DVRs can expose RTSP, but often at low resolution and frame rate. | Make the camera site survey a sales gate. Quantify the share of target stores that pass. |
| C3 | Differentiators: infrastructure-light, local processing, conversion-focused (134–148) | **Mostly not differentiating** | V | **TangoEye (Chennai, acquired by Lenskart in Oct 2023)** markets "turn your existing CCTV into in-store analytics, no new hardware", including footfall, conversion, queue and empty-shelf alerts (V). Footfall-to-transaction "conversion" is a standard metric across the category. | The defensible edge is **causal measurement of promotion and merchandising profit**: POS-integrated, holdout-based, net of discount, cannibalisation and pull-forward, delivered as actions. Treat CCTV as an input, not the moat. |
| C4 | Edge processing minimises cloud cost and bandwidth (140) | **True but shifts the cost** | I | It cuts cloud video cost. It adds hardware capex, field installs, power/UPS dependencies and remote support. The report's pre-mortem names the "hardware support swamp" as the top failure mode (R). | Price and plan for field operations. Use a remote-managed appliance with health monitoring. |
| C6 | Decision engine combines behaviour with POS, inventory, pricing and promotion data; POS measures effectiveness (58, 78) | **Assumed, not shown** | R + I | The whole closed loop depends on getting clean, line-item POS, purchase-cost and promotion data. Desktop POS systems may not offer APIs, and cost data is often incomplete. The reports list blocked or poor POS access as a kill risk (R). | Make POS export rights and a data-quality test a pre-pilot gate. Build a CSV/backup-export path as well as an API path. |
| C5 | Partnerships with CCTV installers and POS vendors (206) | **Sound; with a conflict risk** | I | POS vendors such as GoFrugal/Zoho can bundle basic promo reports (R). | Treat the POS vendor as both channel and competitor. Secure written data-access terms early. |

### D. Business model, metrics, pilot

| # | Claim (line) | Verdict | Tier | Evidence / reasoning | Fix |
|---|---|---|---|---|---|
| D1 | Tiered per-store SaaS, pricing validated via pilots (156–172) | **No numbers given** | R | The earlier cost report proposes test prices of ₹3,500–5,000/store/month (POS-only) and ₹7,500–9,500 (core). Its worked example uses ₹8k–11.5k/store/month plus setup. On that arithmetic the retailer needs about ₹12.5k–17k/store/month of verified extra gross profit just to break even in year 1 (R). These are hypotheses. | Publish *test* price bands and the break-even uplift they imply, so pilots have a pass mark. |
| D2 | Pilot with one supermarket in selected aisles, using control periods or randomisation (190–194) | **Probably too small to prove lift** | I | One store and a few aisles over a few weeks likely give little statistical power. Seasonality and festivals in Tamil Nadu (Pongal, Deepavali) can swamp small effects. | **Size the pilot with a power calculation** on the week-to-week variance seen in Phase 0 POS exports. Use category- or week-level holdouts and switchback design. Pre-register the metric and the minimum detectable effect. Exclude festival weeks or model them. |
| D3 | Primary metric is incremental retailer profit (222) | **Correct** | I | This is the strongest line in the document. | Keep it, and define it formally (see v2 §7). |
| D4 | CV accuracy as a success metric (218) | **Correct, but needs a protocol** | I | Accuracy is meaningless without ground truth. | Use manual-count ground truth per camera at commissioning, and re-check monthly. |

### E. Privacy and long-term vision

| # | Claim (line) | Verdict | Tier | Evidence / reasoning | Fix |
|---|---|---|---|---|---|
| E1 | Will account for the Indian DPDP framework (234) | **Correct and time-sensitive** | V | Rules were notified 13 Nov 2025. Consent-manager provisions apply after 12 months (Nov 2026). Most fiduciary obligations apply from about 13 May 2027. §9(3) bars tracking or behavioural monitoring of children, and the Schedule allows penalties up to ₹200 crore for child-data breaches (V; confirm against the Gazette). | Design to the May 2027 regime now. Add counsel questions on whether transient on-device processing of shoppers, including minors, counts as "tracking". Consider WhatsApp/TRAI marketing-consent rules for follow-ups. |
| E2 | Cross-store category intelligence as platform grows (242) | **Needs contractual basis** | I | Retailers own their sales data. Aggregation requires explicit data-rights clauses, and FMCG-facing products need retailer consent. | Put aggregation rights into the MSA from day one, as opt-in with anonymised and aggregated outputs. |
| E3 | "Turning passive surveillance infrastructure into an active retail growth tool" (256) | **Risky phrasing** | I | Leading with "surveillance" invites privacy backlash from shoppers, the press and regulators. | Use "the cameras and billing data you already own". |

---

## 5. What the concept gets right (keep these)

- No facial recognition; anonymous by default (228).
- SKU recognition and automated personal targeting are deferred from the MVP (186).
- Retailer-defined guardrails on margin, inventory and budget (74).
- Incremental profit, not discount volume, as the north star (222).
- Control periods and randomisation are already mentioned (194). They only need sizing.

## 6. Top 5 changes, in priority order

1. **Resolve the identity contradiction.** CCTV produces aggregates only. Personalisation comes only from POS and explicit QR/WhatsApp opt-in.
2. **Re-centre the wedge on POS-led promotion incrementality.** It needs no hardware and fits the "incremental profit" metric already chosen. CCTV zone analytics follows as a supporting module.
3. **Downgrade "Lost-Sale Radar" to an "Attention-to-Sales Gap" diagnostic** that triggers tests and never directly attributes rupees.
4. **Narrow the ICP to 5–50 store regional chains** and gate sales on a camera and POS site survey.
5. **Size the pilot with a power calculation** (holdouts, a pre-registered metric), make POS data access a pre-pilot gate, and publish test prices with a break-even uplift.

## 7. Sources checked in this audit

- TangoEye product and positioning: [tangoeye.ai](https://tangoeye.ai/)
- Lenskart acquires TangoEye (Oct 2023): [YourStory](https://yourstory.com/2023/10/lenskart-acquires-ai-driven-computer-vision-startup-tango-eye), [Business Standard](https://business-standard.com/companies/news/lenskart-acquires-computer-vision-startup-tango-eye-for-undisclosed-amount-123103000624_1.html), [Inc42](https://inc42.com/buzz/lenskart-acquires-ai-powered-analytics-startup-tango-eye/amp)
- DPDP Rules 2025 notification and phasing: [YourStory](https://yourstory.com/2025/11/digital-personal-data-protection-dpdp-rules-2025-rollout-over-18-months), [AZB & Partners](https://www.azbpartners.com/bank/update-indias-digital-personal-data-protection-framework-comes-into-effect/), [Storyboard18](https://www.storyboard18.com/amp/digital/breaking-govt-notifies-final-dpdp-rules-sets-staggered-rollout-with-key-obligations-for-data-fiduciaries-84201.htm), [Mondaq](https://www.mondaq.com/india/privacy-protection/1708830/dpdp-act-compliance-mandate)
- DPDP §9 (children): [dpdpa.com §9](https://dpdpa.com/dpdpa2023/chapter-2/section9.html), [CyberPeace](https://cyberpeace.org/resources/blogs/prohibition-of-behavioral-tracking-and-targeted-advertising-for-children-under-the-dpdp-act-2023), [DPDP Wiki §9](https://dpdp.myndsolution.com/wiki/act/section-9-processing-of-personal-data-of-children/)
- Grabango shutdown (Oct 2024): [Retail Dive](https://www.retaildive.com/news/grabango-ceases-operations/729628), [Grocery Dive](https://www.grocerydive.com/news/grabango-ceases-operations-automation/729590/)
- Amazon Just Walk Out / India review figure (disputed by Amazon): [Business Standard](https://business-standard.com/companies/news/amazon-s-just-walk-out-checkout-tech-was-powered-by-1-000-indian-workers-124040400463_1.html), [Entrepreneur](https://entrepreneur.com/business-news/amazon-swaps-just-walk-out-for-smart-carts-at-fresh-stores/472305)
- India CCTV analog vs IP share (estimates disagree): [Mordor Intelligence](https://www.mordorintelligence.com/industry-reports/india-cctv-market), [IMARC](https://www.imarcgroup.com/india-cctv-market), [Counterpoint](https://counterpointresearch.com/ko/insights/cctv-firms-brace-for-disruption-as-india-tightens-regulations)

**Not verified in this audit (carry as hypotheses):** NVR outgoing-bandwidth caps; CV accuracy bands for reach and pickup; Jetson decode limits; all price points; promotion-waste statistics. The McKinsey/Nielsen "59% of trade promotions lose money" figure describes **CPG manufacturers' trade spend**, not a retailer's own promotions, so it should not be quoted as retailer evidence without that caveat.

---

## 8. Addendum — audit of Concept v3 (camera-first), 13 Nov 2026

| # | v3 claim | Verdict | Tier | Fix applied in v3 |
|---|---|---|---|---|
| V1 | One shelf-facing camera gives Looked, Stared and Reached | **Not possible with one view.** A shopper-facing camera cannot see the shelf. A shelf-facing camera sees the backs of heads. Reach needs a side angle. | I | Camera roles split per bay: A = shopper-facing, B = side-angle (optional), C = existing CCTV (§4). Brand blocks mapped on a separate shelf photo plus calibration. |
| V2 | "Look-to-Buy = sales ÷ shoppers who looked" | **Implied per-shopper linkage** (repeats A1/B3). The website used a different formula. | I | One definition everywhere: **sales per 100 looks**, brand-level, same hours, from billing totals. No shopper is linked to a sale. |
| V3 | Head direction resolves to brand blocks | **Core untested assumption.** Eye movement without head turn blurs adjacent and stacked blocks. | I | Added **Gate 0 bench test** before any store (§4a). |
| V4 | Attention analytics is new | **Not new.** Eyeware GazeSense sells 3D-camera shopper eye tracking (about 1.3 m range; vendor claim). Intel published open-source code that counts head-pose "lookers" from a shelf-mounted camera. Academic work and a Retail Gaze dataset exist. | V | Competitor scan still needed. Differentiate on brand-block mapping, billing join, India pricing and brand-funded shelves. |
| V5 | Economics | **Missing** in v3 | — | Added an economics table, kill gates and brand data-rights rules (§4b). Values still to be filled. |
| V6 | Face recognition as a later module | **Conflicts with B5** (no CCTV-to-identity link). Founder has left it open. | I | Kept as gated and optional; B5 still holds for the core product. |

Sources for V4: [Eyeware GazeSense for shopper research](https://eyeware.tech/shopper-research/), [Intel shopper-gaze-monitor](https://github.com/intel-iot-devkit/shopper-gaze-monitor-python), [Retail Gaze dataset](https://docs.voxel51.com/dataset_zoo/datasets_hf/retail_gaze.html), [NIJ: What are customers looking at?](https://nij.ojp.gov/library/publications/what-are-customers-looking).
