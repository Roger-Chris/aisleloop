# AISLELOOP — Concept v3 (camera-first)
### See what shoppers look at. Sell what they want.

**Version:** 3.0, 13 November 2026. This version replaces v2's billing-first approach with a camera-first one, following the founder's decisions of 13 Nov 2026. Audit references: [AISLELOOP_CLAIMS_AUDIT.md](AISLELOOP_CLAIMS_AUDIT.md). v2 is kept as the fallback in [AISLELOOP_CONCEPT_v2.md](AISLELOOP_CONCEPT_v2.md).

> **Evidence labels:** **V** = verified on the web; **R** = from earlier research reports, unverified; **I** = inference; **H** = hypothesis to test.

---

## 1. Founder decisions captured in this version

| # | Decision | How v3 applies it | Remaining risk |
|---|---|---|---|
| 1 | No face recognition in the core product. It may be an advanced feature with new cameras later. | Core = anonymous. Face recognition becomes a **Phase 3, opt-in-enrolment-only, counsel-approved** module (§7). | Matching enrolled faces still means scanning every face, including non-consenting shoppers and children (I). This is the highest legal and PR risk in the plan. **Status: open, founder to decide later.** |
| 2 | AI runs head tracking to see where shoppers look and stare. Cameras cover brand areas, not single products, so optimisation is by brand. | **Shelf Attention Engine**: head-pose estimation on shelf-facing cameras, mapped to **brand blocks**, gives looks, stare time and Look-to-Buy per brand (§3). | Minimum brand-block width per camera must be set at commissioning (H). Looking is still not purchase intent. **Status: resolved by brand-block design.** |
| 3 | Work around the issues; we need a good entry strategy. | Entry = a free **"Shelf Attention Audit"** on 2–4 end-caps (§6). | — |
| 4 | Use existing cameras if suitable, otherwise add new ones. | Existing CCTV gives traffic, dwell and queues. **New shelf-facing cameras** handle attention (§4). | Hardware cost and installs per store (H). |
| 5 | Any store first, then complexes (malls), then expand. | Phase 1 is one design-partner store of any size (§6). | — |
| 6 | The single-store pilot is to show traction. | The pilot is pitched as a **demo and design-partner case**, not statistical proof (§6). | Investors and chains will ask for a controlled result before paying at scale. **Status: accepted.** |
| 7 | Get billing data, or build our own billing software. | **Integrate first** (export/CSV/bill parser). Build a lightweight billing app only for stores with no usable POS (§5). | A full POS is a crowded market and a large build (I). **Status: accepted (integrate first, Lite Billing as fallback).** |
| 8 | Kids can be ignored or faces blurred; processing stays in-store, so no data leaves. | Faces are processed in memory only. Stored and exported data is aggregate, and any saved frame is blurred (§8). | On-premise processing **does not exempt** the store from DPDP (I; confirm with counsel). "Ignoring kids" requires detecting age, which is itself processing children's data (I). **Status: DPDP point accepted; children point deferred by founder.** |

## 2. The product in one paragraph

AisleLoop puts small shelf-facing cameras on selected shelves and end-caps, and reuses existing store CCTV where it is good enough. An in-store edge box runs the AI locally. It measures how many shoppers pass a shelf, how many **look at it**, how long they look, and whether they reach for it. It then compares that with what sold, so the store manager can see which shelves attract attention but fail to sell, and which promotions and placements turn looks into sales. Video never leaves the store; only aggregate numbers do.

## 3. Core feature — the Shelf Attention Funnel

For every monitored shelf bay, AisleLoop reports a funnel, the physical-store version of an e-commerce funnel:

| Stage | Signal | Camera needed | Feasibility |
|---|---|---|---|
| **Passed** | Person enters the aisle zone | Existing CCTV | Feasible (I/R) |
| **Stopped** | Dwells ≥ N seconds in the bay zone | Existing CCTV or new | Feasible (I/R) |
| **Looked** | Head oriented toward a mapped **brand block** in the bay | **New shelf-facing camera**, face visible | Feasible at brand-block level when blocks are wide enough and the face is large enough in the frame (I) |
| **Stared** | Look sustained ≥ N seconds | New shelf-facing camera | Same as Looked |
| **Reached** | Arm extends into the bay | **Separate** side-angle camera near the shelf (optional) | Possible on good angles (R) |
| **Sold** | Units of the brand sold in the same hours | POS data | Depends on POS access (§5) |

> The camera stages count events. The Sold stage comes from billing totals for the same brand and hours. **No shopper is linked to a sale.**

**Key metric (one definition everywhere):** *Sales per 100 looks* per brand = units of the brand sold in the monitored hours ÷ looks at the brand's block in the same hours × 100. It is a brand-level aggregate, not a per-shopper conversion [Audit A1, B3].

- A **high-look, low-buy** bay is a candidate for action: price, pack, placement or stock problem.
- This is still a *signal to test*, not a proven lost sale [Audit B3]. But "they looked and didn't buy" is a much stronger pitch than "they stood nearby".

### Brand-block mapping (how attention becomes brand-level)

Products are not tracked one by one. Each monitored shelf is divided into **brand blocks**: the area each brand occupies, which is usually a vertical or horizontal run of facings. Head direction is mapped to the block in view.

- **Setup:** the shopper-facing camera cannot see the shelf it sits on, so brand blocks are mapped separately.
  1. Calibrate the camera's position and angle against the shelf plane.
  2. Take a straight-on reference photo of the shelf.
  3. Mark each brand's block on that photo.
  4. At runtime, the AI projects each head-direction ray onto the shelf plane and assigns it to the block it hits.
  5. Re-mark the blocks whenever the shelf is re-merchandised.
- **Output per brand:** looks, stare time, sales per 100 looks, and share of attention versus share of shelf and share of sales.
- **Known limit:** people move their eyes without turning their head. That limit hits hardest between blocks stacked vertically or placed side by side (I).
- **Brand optimisation:** compare brands in the same bay. For example, Brand A gets 40% of the looks but 15% of the sales, which points to a price, pack or stock issue. Test facing changes, eye-level versus bottom-shelf placement, and promotions per brand.
- **Design rule:** a brand block must be wide enough for head direction to tell it apart from its neighbours. Establish the minimum width per camera position during commissioning by testing against labelled clips (H). Narrow blocks are merged or reported as "mixed".
- **Brand-facing product:** the same per-brand numbers feed the brand-funded shelf offer (§6 Step 2).

**Accuracy honesty:**
- Head pose resolves to a brand block, not a single SKU within it (I).
- Single-product gaze would need eye-tracking-grade, eye-level cameras per shelf. That is a later research item.
- Validate per camera against manually labelled clips before quoting accuracy to customers.

## 4. Hardware

**Cameras per monitored bay:**

| Camera | Mount | Gives | Required? |
|---|---|---|---|
| A. Shopper-facing | On the shelf header or opposite gondola, about head height, looking **out** at shoppers | Looked, Stared (head direction projected onto the shelf) | Yes |
| B. Side-angle | Aisle end or ceiling, oblique to the shelf face | Reached; also Passed and Stopped | Optional; reuse CCTV if it qualifies |
| C. Existing CCTV | As installed | Passed, Stopped, queues, footfall | If it passes the site survey |

Minimum = **1 new camera per bay** (A), plus existing CCTV for traffic. Full funnel = **2 per bay** (A + B).

| Item | Notes |
|---|---|
| Edge box (in-store) | Runs all AI locally. Size it to the camera count, and benchmark before quoting (R/H). |
| Stream source | Pull from the cameras or the PoE switch, not the NVR, where possible (R) |
| Cost per bay | To be quoted: camera A, optional camera B, mount, cabling, share of the edge box (H) |

## 4a. Gate 0 — bench test before any store

Run this before the design-partner pilot. It costs one camera and a mock shelf.

1. Mount camera A on a mock or real shelf with 4–6 brand blocks of realistic widths, both side by side and stacked.
2. Record volunteers looking at known blocks. Hand-label which block each look was aimed at.
3. Measure block-level accuracy for each block width and each camera distance.
4. **Pass:** look counts per block are accurate enough to rank brands correctly (target to be set, H).
5. **Fail:** merge blocks, move the camera, or drop to shelf-section level, and update the pitch.

## 4b. Economics (to fill before the paid pilot)

| Item | Value | Status |
|---|---|---|
| Hardware cost per bay (A, or A+B) | — | H: get quotes |
| Edge box cost per store | — | H: benchmark |
| Install time per bay | — | H: measure in pilot |
| Price to store (per bay or per store per month) | — | H: test in pilots |
| Price to brand (per display per month) | — | H: test with 2–3 brands |
| Break-even bays per store | — | Compute once the rows above are filled |

**Kill / pivot gates:**
- Gate 0 fails at the block level and the section level is not saleable.
- The design-partner store sees no action it would pay for.
- No brand pays for display data after seeing a sample report.
- Hardware plus install cost per bay cannot be recovered within 12 months at the tested price.

**Data rights for brand-funded shelves (write into contracts):**
- The store owns all data from its shelves.
- A brand sees its **own** looks and sales in full.
- A brand sees competitors only as an aggregate ("rest of shelf"), never named, unless the store and those brands agree.
- AisleLoop may use anonymised, aggregated data across stores only with the store's opt-in.

## 5. Sales data (Bought stage)

In order of preference:
1. **Export from the store's POS** (CSV or scheduled backup). This works with most systems, without needing an API (I).
2. **Partner APIs** (GoFrugal/Zoho, Logic ERP, Ginesys — R) once the product has traction.
3. **AisleLoop Lite Billing**: a simple billing app only for stores with no usable POS. This is the fallback, not the product.
4. **Manual category sales entry** for the very first demo store, if nothing else works.

> Building a full POS competes with established vendors and diverts the team from the AI product (I). Keep billing minimal unless it becomes the distribution channel.

## 6. Entry strategy

**Step 1 — Design-partner store (traction).**
- Pick any supermarket willing to host 2–4 shelf cameras for about 4–6 weeks, free.
- Choose bays with an active promotion or a premium brand.
- Deliverable: a **Shelf Attention Report** showing passed, looked, stared and bought per bay, plus a before/after on one change (move, price or display).
- Use this as the demo, the case study and the pitch-deck evidence. Label it honestly as one store with no control group.

**Step 2 — Brand-funded shelves.**
- FMCG brands already pay stores for end-caps and displays.
- Offer them "proof your display gets looked at" per bay. The brand pays for the cameras on its own display, and the store gets the insight free.
- This can fund hardware without the store paying (H, a strong candidate wedge to test).

**Step 3 — Paid store pilots.**
- Run 2+ stores with a test bay and a control bay, sized from Step 1 data.
- Charge a pilot fee, credited on conversion.

**Step 4 — Complexes, malls and chains.**
- Roll out multi-store and mall deployments.
- Partner with CCTV installers as resellers.

## 7. Advanced features (later, gated)

| Feature | Gate before launch |
|---|---|
| Opt-in personalised offers (QR/WhatsApp sign-up, with offers based on purchase history) | Consent flow, marketing-message rules |
| Face recognition for enrolled loyalty members (new cameras) | **Explicit enrolment consent** and written counsel sign-off. Non-enrolled faces are processed in memory only and discarded. A child-exclusion policy is required. A public opt-out is required. Treat it as optional, and drop it if counsel says no. |
| Single-product gaze (eye-level cameras) | Accuracy benchmark ≥ target on labelled data |
| Digital-display triggers | Store has screens |

## 8. Privacy design

- **All video is processed in the store.** Frames live in memory only. Faces are used for head direction and then discarded. Nothing identifiable is stored or sent.
- **Anything saved is blurred** (for example debug clips or proof images): faces are blurred at capture. Only counts and ratios leave the store.
- **Notice signage** at the entrance and on monitored shelves.
- **Children:** the system does not build any profile of any individual, child or adult. Counts are aggregate. Age is **not** estimated, because estimating it would itself be processing children's data (I).
- **Law (V):** the DPDP Rules 2025 were notified on 13 Nov 2025, with most obligations from about 13 May 2027. §9(3) bars tracking or behavioural monitoring of children.
  - **On-premise processing reduces breach risk. It does not remove the store's obligations** (I).
  - **Counsel question:** does transient head-pose analysis of every shopper count as "behavioural monitoring" of children when no individual profile is kept?

## 9. Pitch

AisleLoop shows supermarkets and brands which shelves shoppers actually **look at**, how long they stare, and how many of those looks turn into sales. It uses small shelf cameras and the store's existing CCTV, with all AI running inside the store and no faces stored. It turns "they looked but didn't buy" into specific fixes in price, placement and promotion.

---

## Appendix — what changed from v2

| v2 | v3 |
|---|---|
| Billing-data-first, cameras optional | Cameras-first; billing data feeds the "Bought" stage |
| Existing CCTV only | Existing CCTV plus new shelf-facing cameras |
| Dwell-based "Attention-to-Sales Gap" | Head-pose "Look-to-Buy" funnel per brand block, enabling brand-level optimisation |
| No face recognition, ever | Not in the core product; later opt-in enrolment module, counsel-gated |
| Regional chains, 5–50 stores | Any design-partner store, then complexes and malls, then chains |
| Pilot sized for statistical proof | First pilot = traction demo; paid pilots get a control group |
| New: brand-funded shelf cameras as a hardware-funding wedge (H) | |
