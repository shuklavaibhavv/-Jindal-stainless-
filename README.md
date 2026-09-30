# StainlessCarbon — Carbon & Energy Calculator for Stainless Steel

Round 1 + Round 2 entry for **"Stainless Spark – Engineering Innovation, Building Futures"** (Jindal Stainless, via Unstop; engineering track) — **Problem Statement 3:** build a tool that estimates carbon emissions per tonne of stainless steel from the input mix and energy source, and helps users find lower-carbon combinations that remain practical.

**Live calculator:** https://grv-io.github.io/sc-346a9f98/website/

## The idea in one line

Stainless steel's carbon is an **alloy-chain problem** — ferrochrome and nickel drive 60–75% of the footprint on virgin/NPI routes (nickel alone 36% of JSL FY26's total, 51% once ferrochrome including JSL's own furnaces is added) — and stainless-specific tools rarely model captive power and CBAM's direct-only boundary together. We built one that does: calibrated to India's real grid and captive-coal economics, bounded by metallurgical guardrails, and priced per tonne of coil, per installation, at the EU border (CBAM).

## What's here

| Path | Contents |
|---|---|
| `case-study/StainlessCarbon_Round2.pdf` | The 8-page Round 2 deck (`.pptx` editable; `build_ppt_round2_detailed.py` builds it from live model numbers in `round2_numbers.json`; `video_cards/` are its 1920×1080 page renders). `StainlessCarbon_Round2_videocards.pdf` is the earlier light version (`build_ppt_round2.py`, renders in `video_cards_light/`) |
| `case-study/StainlessCarbon_Round1.pptx` | The 2-slide Round 1 executive summary (as submitted) |
| `case-study/build_ppt.py` | Generates the Round 1 deck as native, editable PowerPoint (python-pptx) |
| `appendix.pdf` | 8-page research appendix (built from `appendix.html`) |
| `internal/` | Team-only working files: study guide, video script and shot guide, audit and planning notes (see `internal/README.md`) |
| `website/` | The live calculator — open `index.html` in any browser. `model.js` is the single source of truth for every emissions number; `business.js` turns the CBAM figure into what actually reaches JSL. `model.test.mjs` runs 415 checks (`node website/model.test.mjs`), `business.test.mjs` runs 110 more (`node website/business.test.mjs`); `MODEL_NOTES.md` documents every constant, formula and status (JSL-assured / literature / derived / assumption / calibration-fit), plus a §7 list of what we could not make fully honest |
| `research/` | 19 research files (18 evidence files + the canonical model) behind every number — emission factors, ISSF methodology, CBAM regulation, Jindal disclosures, competitor teardown |
| `research/22-canonical-model-v1.md` | **Start here.** The locked single source of truth (now v4; filename kept for link stability — see the file's own header): every figure used in the deck, calculator and appendix traces to this file, which in turn traces to `website/model.js` and `website/business.js` |
| `competition-brief.md` | The problem statement and evaluation criteria |
| `DEPLOYMENT.md` | How the live calculator is hosted and updated |

## The model, briefly

```
S1 = captive power (264 MW x load factor, baseload, a costed lever) + SAF ferrochrome reductant
     + fired fuel (per-installation, cleanfuel-substitutable) + AOD (process + carbon mass balance)
S2 = purchased power (the swing supply, after captive) x (1 - RE) x grid factor
S3 = (1 - scrap%) x merchant-share alloy mass x emission factor
     + Ni units + FeMo + FeMn + Cu + Fe-units + consumables
     subject to: grade-specific scrap caps (304 80% / 316 75% / 430 60% / 2205 60% / J4 70%)

CBAM figure (per installation, per t coil) = S1 - captive-power CO2 - precursor SAF reductant shipped out
                                             + purchased-precursor direct emissions (FeCr, NPI, FeMn, pig iron)
```

- Grade-aware charge chemistry (304 / 316 / 430 / 2205 / **J4**, 200-series), with nickel-source, ferrochrome-sourcing (own SAF vs merchant, partial split) and SAF-technology/biochar choices
- India-specific factors: grid 0.710 tCO2/MWh (CEA v21.0), captive subcritical coal 1.045, Odisha ferrochrome 5.4–5.9 tCO2/t (ICDA); own-furnace ferrochrome (partial captive/merchant split) sits inside Scope 1+2 on its captive share, Scope 3 only prices the merchant share
- Boundary discipline: CBAM counts **direct** emissions only — captive power is excluded even though it's Scope 1; purchased precursors' direct emissions are included. Renewables and the captive load factor provably do not move the CBAM figure; grade does
- **Calibration:** the FY26 preset reproduces JSL's disclosed S1+2/total/energy **within 0.1%** (seven fitted constants to seven FY26 targets — an identity, not proof), and Jajpur/Hisar S1+2 exactly by construction. The real evidence is out-of-sample: FY24 back-cast is within 0.65% S1+2 (Scope 1 alone within 0.8%)
- CBAM module: per-installation figure, per tonne of coil, 2026–2030 phase-in, EU default (7.14 → 8.44 tCO2/t with markup) vs Jajpur's verified figure (1.171/t coil, 2026) — a buyer-side saving of **₹135–161 Cr/yr in 2026 at 40 kt (India's national quota is 64,073 t/yr for all mills)**, rising to ₹265–285 Cr/yr by 2030; of that, **≈₹139 Cr/yr reaches JSL directly** once Iberjindal's own volume, pricing capture and verification cost are worked through (`business.js`)
- Indonesia melt-shop route modelled and compared: filing its EU default is worse than filing India's own default (+€619/t coil), so the model flags misrouting rather than assuming it's clean
- Three optimiser tiers (practical today / stretch 2030 / theoretical floor), each with disclosed, rising marginal $/tCO₂ costs — hydrogen priced at $250/tCO₂, not lumped in with biochar

## Sources

JSL ESG Factsheets, BRSR and CDP disclosures FY22–26 · CEA CO2 Baseline Database v21.0 · BEE PAT · IPCC 2006 · worldstainless/ISSF · ICDA ferrochrome LCA · EU Regulations 2023/956, 2025/2620-21, 2026/1740, 2026/1457 · Goldman Sachs, ICRA, CRISIL analyst reports.

*Independent case-competition project built on public disclosures — not an official Jindal Stainless product.*
