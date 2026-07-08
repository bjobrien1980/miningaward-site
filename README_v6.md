# Mining Industry Award 2020 — Entitlements Calculator
### Change Log

**Award reference:** MA000011  
**Publisher:** Briefer Pty Ltd  
**Deployed at:** [https://miningaward.briefer.com.au](https://miningaward.briefer.com.au)

---

## Version 6 (`index_v6.html`)

**Status:** Current production version

### New feature — Public holiday clustering / maximum payment test
Tests whether an annual salary (TFR), averaged per pay period, covers the Award entitlement arising in the **highest penalty rate period** under the selected State/Territory public holiday calendar, on the basis that each pay period must be satisfied standalone — above-Award payments in other pay periods are not set off against the Award obligation arising in that period.

**New input (Step 1):** Pay period selector — Weekly / Fortnightly / Monthly (default: Fortnightly).

**New results card (Step 3):** "Public holiday clustering — maximum payment test" — a show/hide toggle card matching the Employer on-costs pattern (hidden by default), showing:
- The highest penalty rate period found in a 2026–2045 scan of the WA calendar, with the specific dates and holidays, and the years in which the maximum recurs
- The recurring Easter–ANZAC cluster from 2028 (displayed separately where the overall maximum is a different cluster)
- Average Award entitlement per pay period vs the highest penalty rate period entitlement, and the uplift
- Salary test against the TFR entered in the hero panel (live-updating): salary per period, margin vs the highest penalty rate period (pass/fail), and the minimum annual salary required to clear that period standalone

Methodology and calendar assumptions are documented in the Assumptions & limitations card rather than within the test card itself.

### Multi-jurisdiction calendars (State/Territory selector)
The PH clustering card includes a State/Territory dropdown (default: WA). Calendars for all eight jurisdictions are generated from each jurisdiction's public holidays legislation as at July 2026 and validated against the Fair Work Ombudsman's published 2026 lists and official state sources (wa.gov.au, SafeWork SA, WorkSafe Tasmania):

- **Weekend rules:** Tasmania *substitutes* weekend-falling holidays (the following weekday is the holiday instead); all other jurisdictions *add* a day when New Year's Day, Christmas Day or Boxing Day (SA: Proclamation Day holiday) falls on a weekend. Australia Day moves to the Monday in all jurisdictions.
- **ANZAC Day:** additional Monday in WA (statutory) and ACT; NSW per the announced 2026–2027 trial only; nowhere else.
- **Easter:** Easter Saturday and Easter Sunday in NSW, VIC, QLD, SA, NT, ACT; WA has Easter Sunday (2022) and gains Easter Saturday from 2028; Tasmania has neither (restricted Easter Tuesday excluded).
- **State days:** NSW Labour Day (1st Mon Oct); VIC Labour Day (2nd Mon Mar), AFL Grand Final Friday (proclaimed, approximated as last Friday September), Melbourne Cup (metro); QLD Labour Day (1st Mon May), King's Birthday (1st Mon Oct); SA Adelaide Cup Day (2nd Mon Mar); TAS Eight Hours Day (2nd Mon Mar); NT May Day and Picnic Day; ACT Canberra Day and Reconciliation Day (Monday on/after 27 May).
- **Exclusions:** regional/local holidays (Qld show days incl. Ekka, Tas regional days, NT show days, Vic non-metro Cup substitutions, Port Hedland/Karratha King's Birthday), part-day holidays (SA/NT/Qld Christmas Eve, SA/NT New Year's Eve), bank-only holidays (NSW Bank Holiday).

**Cross-jurisdiction scan results (fortnightly, 2026–2045):** 6 PHs in the Christmas-on-Saturday year-end cluster for every jurisdiction except Tasmania (3 — substitution semantics thin every cluster). Monthly maxima: 6 for NSW and ACT (April 2026, via the ANZAC Monday), 6 for WA (April 2037), 5 for VIC/QLD/SA/NT, 3 for TAS.

### Employer on-costs — superannuation on OTE, per allocation
The superannuation line is now computed on a full ordinary time earnings base (SGAA s 6, SGR 2009/2) rather than the previous shift-pay-only approximation:
- **OTE =** ordinary-hour shift pay (including shift and weekend penalties attaching to ordinary hours) + annual leave pay and loading + public holiday entitlement + all-purpose allowances. Overtime is excluded.
- OTE is computed **separately for the standard and optimised allocations** from the same per-day rows that drive the day-by-day breakdown. Because the optimised (employer-minimum) allocation assigns ordinary hours to the lowest-penalty shifts and pushes more paid hours into overtime, its OTE base is structurally smaller — producing an SG saving *additional to* the shift-pay saving shown in the hero panel. The card displays both OTE bases and the annual SG saving.
- Caveats documented in-app: annual leave loading treated as OTE unless demonstrably referable to lost overtime; expense-type allowance components should be excluded; maximum contribution base not applied (Award-level OTE sits below it); payroll tax and workers' comp remain indicative flat rates on total entitlement (payroll tax on super contributions not modelled).

### Results layout reordered
Step 3 cards now render in the following order:
1. Estimated annual Award entitlement (hero, incl. optimised figure and TFR comparison)
2. Rate build-up
3. Per-cycle breakdown
4. Continuous shiftworker assessment (O'Neill)
5. Public holiday clustering — maximum payment test *(show/hide toggle, hidden by default)*
6. Employer on-costs *(show/hide toggle, hidden by default)*
7. Day-by-day breakdown *(show/hide toggle, hidden by default)*

### Day-by-day breakdown — toggle card with allocation switch
- The day-by-day table is now a show/hide toggle card matching the on-costs pattern (hidden by default)
- Inside the card, a switch toggles the on-screen view between **Standard (equal)** and **Optimised (employer-minimum)** allocations; each view carries its own descriptor line (the optimised view cites the cl 12.5(d) assignment order)
- The optimised table shows per-day ordinary/overtime apportionment under the employer-minimum assignment — previously only the optimised *total* was visible (in the hero); the day-level detail is new
- **Print / Export PDF now includes both allocations**, regardless of the on-screen toggle and switch state: both tables are always rendered in the DOM (hidden via CSS when not selected) and `@media print` rules force them visible; the allocation switch itself is excluded from print; the card is permitted to break across pages so two full tables print cleanly
- Engine refactor: `computeOptStot` replaced by `computeOptRows`, which returns per-day rows for the optimised allocation (totals unchanged — the optimised annual figure is now derived from the same rows that render in the table, so the hero figure and the day-by-day cycle total reconcile by construction)

### WA public holiday calendar engine
- Easter computed via Butcher's algorithm (Gregorian computus) — deterministic for any year
- Calendar rules per the Public and Bank Holidays Act 1972 (WA), including **Easter Sunday (public holiday since 2022)** and the **additional-day rules**: New Year's Day, ANZAC Day and Christmas Day falling on a Saturday or Sunday generate an *additional* Monday public holiday; Boxing Day on a Saturday generates an additional Monday, and on a Sunday or Monday an additional Tuesday; Australia Day on a weekend is *observed* on the following Monday (single day)
- **2028 regime switch** per the Public and Bank Holidays Amendment Bill 2025: Easter Saturday and Show Day added; Labour Day moves to the second Monday in March (third Monday in state election years — 2029, 2033, 2037, 2041, 2045); King's Birthday moves to the second Monday in June; WA Day moves to the second Monday in November
- Generated calendar validated exactly against the WA Government's published 2026 and 2027 public holiday dates (wa.gov.au), including the ANZAC additional Monday (2026, 2027) and the Christmas/Boxing additional days (2026, 2027)

### Scan findings (2026–2045, pay period alignment capturing the maximum cluster)
| Pay period | Max PHs | When |
|---|---|---|
| Weekly | **5** | Good Friday → ANZAC Day within 7 days: 2030, 2041 |
| Fortnightly | **6** | Year-end cluster in Christmas-on-Saturday years: Dec 2027–Jan 2028, Dec 2032–Jan 2033, Dec 2038–Jan 2039 (Christmas, Boxing, both additional days, New Year's Day + additional day) |
| Monthly | **6** | April 2037 (full Easter block + ANZAC Saturday + ANZAC additional Monday) |

Recurring Easter–ANZAC fortnight cluster from 2028 (Easter Saturday reform): **5 PHs** in 2028, 2030, 2033, 2041, 2044; 4 PHs in most other years (up from 3 pre-reform). Notably, the fortnightly maximum of 6 already arises **before** the 2028 reform commences (December 2027) under the existing additional-day rules.

### Calculation method
- Highest penalty rate period entitlement = (annual Award entitlement ÷ periods/yr) − (smeared annual PH component ÷ periods/yr) + (max PHs × shift length × composite rate × 1.5)
- The incremental 1.5× PH premium is consistent with the calculator's existing cl 21.3 model; it is conservative (overstates) for weekend-falling public holidays whose ordinary hours already attract Saturday/Sunday penalty rates
- Minimum standalone-compliant salary = highest penalty rate period entitlement × periods/yr
- For casuals the premium is applied at the loaded composite rate for worked public holidays (the baseline entitlement excludes non-worked PH payment, per the existing casual model)

### Assumptions & limitations (documented in-app, in the Assumptions & limitations card)
- All clustered public holidays assumed to fall on rostered working days and be worked at full shift length; pay period alignment capturing the maximum cluster assumed; ordinary shift pay per period approximated by the roster-cycle average
- WA calendar only, regardless of the PH count set in Step 1; pre-2028 King's Birthday and Show Day dates are proclaimed annually and approximated (isolated Mondays — cannot affect the cluster maximum); regional variations (e.g. Port Hedland / Karratha King's Birthday) not modelled
- Election-year Labour Day handling assumes WA fixed four-year terms continue

### Print / PDF export report header
Printed and PDF-exported output now opens with a report metadata header (print-only — hidden on screen):
- **Assessment date** — generated at print time (refreshed on `beforeprint`)
- **Rates effective** — 1 July 2026 (PR799292, PR799449), from a named constant so annual rate updates flow through automatically
- **Jurisdiction** — the State/Territory selected in the PH clustering card
- **Reference** — an optional free-text field (file number, cohort, matter name; max 120 characters), entered via a screen-only input above the navigation in Step 3; the row is omitted from the header when blank
- **Advisory disclaimer** — short statement that the assessment is indicative, for information/screening purposes only, not legal or financial advice, and should be verified against MA000011, the Fair Work Act 2009 (Cth) and current FWC determinations

The reference input updates the header live without a full re-render (typing focus preserved), and the value persists across step navigation.

### Housekeeping
- Rate build-up reformatted as a standard results card, matching the Per-cycle breakdown (same header, row and total styling); bespoke grey 11px styling removed
- Pay period selector descriptor text removed from Step 1
- Header HTML comment corrected from stale "v4" label to v6

---

## Version 5 (`index_v5.html`)

**Status:** Superseded by v6

### Annual Wage Review 2026 rate update
All Award rates updated to the figures gazetted in two FWC determinations, both effective **1 July 2026**:
- **PR799292** — Annual Wage Review 2026 (Mining Industry Award 2020)
- **PR799449** — Expense-related allowances 2026 (Mining Industry Award 2020)

No rate in this release was calculated or estimated — every figure was transcribed directly from the determinations and cross-checked against the FWC's own published composite penalty rate tables (cl B.1.3 of PR799292), which reconciled exactly to four decimal places across all eight classifications.

| Constant | v4 (12 Dec 2025) | v5 (1 Jul 2026) | Source |
|---|---|---|---|
| Entry Level weekly rate | $954.93 | $1,004.90 | PR799292 cl 15.1(a) |
| Level 1 weekly rate | $999.77 | $1,047.30 | PR799292 cl 15.1(a) |
| Level 2 weekly rate | $1,037.01 | $1,086.20 | PR799292 cl 15.1(a) |
| Level 3 weekly rate | $1,068.55 | $1,119.10 | PR799292 cl 15.1(a) |
| Level 4 weekly rate | $1,139.61 | $1,193.90 | PR799292 cl 15.1(a) |
| Level 5 weekly rate | $1,214.09 | $1,271.80 | PR799292 cl 15.1(a) |
| Level 6 weekly rate | $1,273.75 | $1,334.10 | PR799292 cl 15.1(a) |
| Level 7 weekly rate | $1,325.05 | $1,388.10 | PR799292 cl 15.1(a) |
| Industry allowance | $39.53 | $41.41 | PR799292 item 3, cl 18.2(b)(i) |
| Electrical licence allowance | $48.61 | $50.92 | PR799292 item 4, cl 18.2(c) |
| Leading hand (3–10) | $47.01 | $49.24 | PR799292 item 8, cl 18.2(f) |
| Leading hand (11–20) | $59.83 | $62.67 | PR799292 item 8, cl 18.2(f) |
| Leading hand (20+) | $80.45 | $84.27 | PR799292 item 8, cl 18.2(f) |
| First aid allowance | $21.37 | $22.38 | PR799292 item 7, cl 18.2(e) |
| Underground allowance | $1.97/hr | $2.06/hr | PR799292 item 9, cl 18.2(h) |
| Tool allowance | $17.86 | **$17.86 (unchanged)** | PR799449 item 2, cl 18.3(b) |

### Notable findings
- **Entry Level increased 5.23%, not the headline 4.75%.** This reflects the FWC's structural adjustment phasing out the C13/C14 classification levels, the first stage of which took effect from 1 July 2026. Levels 1–7 increased by approximately 4.75% each, consistent with the general AWR determination. This was flagged as a risk *before* the determinations were published — confirming that calculating new rates via a flat percentage multiplier would have understated the Entry Level rate.
- **Tool allowance did not increase.** It is an expense-related allowance varied under a separate mechanism (PR799449, s 157 Fair Work Act) rather than the wage-related AWR adjustment, and was republished at the same $17.86/week figure.

### Clause reference correction
- Industry allowance clause reference in the rate build-up panel corrected from the generic `cl 18.1` to the precise `cl 18.2(b)(i)`.

### Description text updates
- All allowance description strings in Step 1 (electrical licence, first aid, underground, leading hand tiers) updated to display new dollar figures.
- "Rates effective" date updated from 12 December 2025 to **1 July 2026** in the page header, HTML comment, and assumptions footnote, with both determination numbers cited.

### Out of scope (flagged, not actioned)
PR799292 also updated the drilling/prospecting/exploration allowances (cl 18.2(d)) and PR799449 updated the overtime meal allowance (cl 18.3(a)). Neither allowance is currently modelled in the calculator. No action was taken as these fall outside the existing feature set, but they are noted here for future scope consideration.

---

## Version 4 (`index_v4.html`)

**Status:** Superseded by v5

### Bug fixes
- **Electrical licence allowance rate corrected** — `ELEC` constant corrected from `$48.81` to `$48.61` per week (the erroneous rate was introduced in v2 and carried through v3). Affects composite ordinary rate for all employees to whom the allowance applies.
- **Dead constant removed** — `S2: 2.00` was defined in the Award constants object but never referenced in the calculation engine. Saturday ordinary hours beyond the first three hours were correctly calculated using `A.SU` throughout. The constant has been removed and `S1` and `SU` now carry inline comments documenting the Award clause each applies to.

### Calculation improvements
- **Superannuation basis corrected** — On-costs superannuation is now calculated on ordinary time earnings (OTE) only, consistent with the Superannuation Guarantee (Administration) Act 1992 s 6. Previously, the 12% rate was applied to the total annual Award entitlement, which overstated superannuation by including overtime pay and other non-OTE components. OTE is calculated as the ordinary-hours-only shift pay component (excluding overtime) plus all-purpose allowances, both of which attract super.

### Clause reference corrections
- **Annualised wage arrangement clause reference updated** — All references to "cl 28" in the outer limit section (section label, inline notes, below-Award warning, assumptions footnote, and code comment) corrected to **cl 17.2**, which is the correct clause governing annualised wage arrangements in the Mining Industry Award 2020.

### Description corrections
- Electrical licence allowance description updated to reflect corrected rate of `$48.61/week`.
- Superannuation on-costs label updated from `"+ Superannuation (12%)"` to `"+ Superannuation (12% on OTE)"` with an updated footnote citing SGAA s 6.
- Assumptions footnote updated to reflect OTE basis for superannuation.

---

## Version 3 (`index_v3.html`)

### Roster changes
- **Rail allowance clause reference corrected** — The allowance label and description were updated to reference **cl 18.2(g)** (mainline competent locomotive drivers — 30% of minimum weekly rate), correcting the erroneous `cl 18.7` reference introduced in v2.

### Error handling
- **Global error handler added** — A `try/catch` block wraps the top-level `render()` call. If a JavaScript error occurs, a formatted red error panel is displayed in the page with the error message and stack trace, replacing the previous behaviour of a silent blank page. This assists in diagnosing edge cases in production.

### O'Neill assessment
- **Public holiday count made dynamic in O'Neill citation** — The O'Neill assessment card now appends the user's configured public holiday count (`st.wph`) to the FWC citation, so the card reads *"Based on [n] public holidays per annum"* rather than a static reference. This ensures the displayed assessment correctly reflects any override of the default WA figure.

---

## Version 2 (`index_v2.html`)

### Bug introduced
- **Electrical licence allowance rate incorrectly changed** — `ELEC` constant changed from `$48.61` to `$48.81` per week. This error persisted through v3 and was corrected in v4.

### New features

#### Employment types
- **Part-time employees** — The calculator now supports part-time employment. Contracted ordinary hours per week can be entered directly or derived from an FTE figure (with two-way sync between the fields). Ordinary hours per shift, annual leave entitlement, and all other calculations are scaled to contracted hours. The FTE figure is displayed in the results hero and descriptor line.
- **Casual employees** — Casual employment is now supported. A 25% casual loading (cl 12.3) is applied to all rates via the `cmpEff` composite rate. Annual leave loading and public holiday entitlement for non-worked days are set to zero for casual employees, consistent with the Award. A plain-English explanation of the casual loading trade-off is displayed when casual is selected.

#### New roster templates
The following roster templates were added, replacing some templates from v1:

| ID | Label | Cycle |
|---|---|---|
| `5d2` | 5 days / 2 off | 1 week |
| `7d7` | 7 days / 7 off | 2 weeks |
| `lfd` | Lifestyle (days only) — 4D/5X/5D/4X/5D/5X | 4 weeks |
| `lfdn` | Lifestyle (days & nights) — 4D/5X/5N/4X/5D/5X/4N/5X/5D/4X/5N/5X | 8 weeks |

The following templates from v1 were removed in v2: `8n6` (8 on / 6 off nights), `14n7` (14 on / 7 off nights), `15d13` (15 on / 13 off), `14n14` (14 on / 14 off nights).

#### New allowance
- **Rail allowance** — Added toggle for the rail allowance (30% of minimum weekly rate, cl 18.2(g)). The clause reference was incorrectly cited as `cl 18.7` in v2; corrected in v3.

#### Employer on-costs
- **On-costs card added** — An optional toggle in Step 3 reveals an employer on-costs breakdown showing superannuation (12%), payroll tax (5.5%), and workers' compensation (1%) applied to the annual Award entitlement, with a total employment cost figure. Rates are noted as indicative in the accompanying footnote. *(Note: superannuation basis overstated in v2 and v3 — corrected to OTE basis in v4.)*

#### Custom roster enhancements
- **Custom cycle length extended** — Maximum cycle length for custom rosters increased from **8 weeks** to **16 weeks**, accommodating longer rotating patterns.
- **Shift length input** — Shift length input now accepts **0.5-hour increments** (e.g. 12.5 hours), with `step=0.5` on the number input and `Math.round(n * 2) / 2` rounding applied on change.

#### Annual leave calculation
- **Greater-of test implemented** — Annual leave loading (cl 22.3) now applies the greater-of test: the higher of 17.5% on ordinary leave pay, or the penalty/loading component the employee would have received if working those leave days. The result method (`"17.5%"` or `"penalty rates"`) is displayed in the per-cycle breakdown with a checkmark when the penalty rates arm applies.

#### UX and descriptive improvements
- **Industry allowance badge** — A static "Always included" badge is displayed next to the industry allowance in Step 1, making clear it is embedded in all calculations rather than being an optional toggle.
- **Permanent night shift description expanded** — The toggle description now includes the full three-limb Award definition from cl 21.2(b): night shift only; or more than 4 consecutive weeks on nights; or a rotation where more than one-third of working time is on nights.
- **Underground allowance description updated** — Clarification added that the allowance does not apply to employees classified as underground miners.
- **O'Neill card references user-configured PH count** — The continuous shiftworker assessment card reflects the user's configured public holiday figure rather than a hardcoded reference.

---

## Version 1 (`index_v1.html`)

**Initial release.** Established the core calculation engine and three-step UX.

### Calculation engine
- Rate build-up from Award first principles: `(cl 15 weekly minimum + cl 18.1 industry allowance) ÷ 38 ordinary hours = composite ordinary rate`
- Electrical licence allowance (cl 18.2) added to composite rate as an all-purpose allowance where applicable
- Ordinary hours per shift derived from Award cl 12.5(d): `(38 × cycle weeks) ÷ working shifts`
- Shift and penalty rate multipliers: weekday day (100%), afternoon/night (115%), permanent night (130%), Saturday first 3 ordinary hours (150%), Saturday remaining ordinary hours / Sunday (200%)
- Overtime rates: non-continuous shiftworker (150% for first 3 hours, 200% thereafter); continuous shiftworker (200% flat, all days) — cl 20
- Annual leave: 4 weeks (standard) or 5 weeks (continuous shiftworker) under the NES; 17.5% loading (cl 22.3)
- Public holiday entitlement: incremental 1.5× on working days (cl 21.3), defaulting to 9 WA public holidays per year
- Allowances: leading hand (three tiers — cl 18.3), first aid (cl 18.4), tool (cl 18.5), underground (cl 18.6)
- **Optimised allocation** — Calculates employer-minimum Award liability by assigning ordinary hours to cheapest shifts first (weekday day → weekday night/aft → weekend), consistent with employer discretion under cl 12.5(d)

### Continuous shiftworker assessment
- **O'Neill v Roy Hill Holdings [2015] FWC 2461** — Automatic assessment against the Williams threshold: ≥34 working Sundays and ≥6 public holidays per year. Manual override available via toggle with flip between continuous/not-continuous.

### TFR comparison
- Optional TFR input producing percentage above/below both the standard and optimised Award allocations

### Annualised wage outer limit
- Buffer calculation showing additional weekly hours absorbable at 150% and 200% OT rates before TFR falls below Award minimum
- Below-Award warning displayed where TFR does not cover standard entitlement

### Roster templates (9 templates)

| ID | Label | Cycle |
|---|---|---|
| `8d6` | 8 on / 6 off | 2 weeks |
| `8n6` | 8 on / 6 off — Nights | 2 weeks |
| `14d7` | 14 on / 7 off | 3 weeks |
| `14n7` | 14 on / 7 off — Nights | 3 weeks |
| `15d13` | 15 on / 13 off | 4 weeks |
| `14d14` | 14 on / 14 off | 4 weeks |
| `14n14` | 14 on / 14 off — Nights | 4 weeks |
| `8d6n7` | 8D / 6 off / 7N / 7 off | 4 weeks |
| `7d7n14` | 7D / 7N / 14 off | 4 weeks |
| `custom` | Custom builder (up to 8-week cycle) | Variable |

### UX
- Three-step flow: Role & Allowances → Roster → Entitlements
- Custom roster builder: tap-to-cycle tiles (Off → Day → Night → Afternoon)
- Granular day-by-day breakdown table with per-rate-column hour apportionment (Ord 100% through OT 200%), colour-coded by rate type with Award clause tooltips
- Rate build-up panel showing derivation from cl 15 weekly minimum
- Print / Export PDF support
- Public holiday count configurable (default: 9 WA)
- Employment type: full-time only *(part-time and casual added in v2)*

---

*This calculator is for information purposes only. It does not constitute legal advice. Refer to the Mining Industry Award 2020 [MA000011] and the Fair Work Ombudsman for authoritative guidance.*  
*© 2026 Briefer Pty Ltd. All rights reserved.*
