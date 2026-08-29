# 📡 Telecom Customer Churn Analysis: A PwC Case Study

> Completed as part of the PwC Switzerland **Power BI / Data Analytics virtual experience
> programme** (Forage). Self-directed case study on a provided dataset — not client work, and the
> figures below describe the sample file only, not any real telecom operator.

**Report deck:** https://canva.link/6g5h3j4m87on5vt

**Analysis:** [`churn_eda_v2.ipynb`](churn_eda_v2.ipynb)

**Data:** [`Data`](Data)

---

## 🎯 Project Overview

**Audience.** Written for the retention lead in a consumer telecom division, with the CFO as
secondary reader — the first needs a list of accounts to act on, the second needs the revenue at
stake sized before funding retention offers.

**Objective.** Move past a single headline churn rate to identify which segments, contracts and
service experiences carry attrition, how much recurring revenue sits behind them, and whether any
observable behaviour flags a customer early enough to intervene.

**In scope.** Descriptive analysis of one customer-level snapshot: segment churn rates, recurring
revenue attached to churners, service and support patterns, high-value churner profiling, and a
transparent additive risk score that runs without a modelling pipeline.

**Out of scope.** Causal attribution, predictive modelling with holdout validation, customer
lifetime value or margin analysis, offer design and pricing, competitor context, and any cohort or
time-series view — the file has no dates.

**Methodology.** Cleaning and encoding, then churn distribution across categorical and numerical
features, correlation checks, revenue quantification, service-bundle and support-ticket analysis,
high-value churner profiling, and a six-flag additive risk score validated against actual churn.
Fourteen numbered sections in `churn_eda_v2.ipynb`.

---

## ▶️ How to run

```bash
git clone https://github.com/thisisChloe/Customer-Churn-PwC.git
cd Customer-Churn-PwC
python -m venv .venv && source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter lab churn_eda_v2.ipynb
```

Then **Kernel → Restart Kernel and Run All Cells**.

The notebook is **strictly sequential**: `df` is enriched in place as it goes (`AddonCount` in section 10,
tech-ticket bands in section 12, `HistoricalRevenue` in section 13, `risk_*` and `RiskScore` in
section 14), so running cells out
of order will raise `KeyError` or silently reuse stale columns. There is no randomness, so a full
run reproduces every figure in this README exactly.

Paths are relative to the repository root; the Excel file must stay in `Data/`.

---

## 🔍 Key Findings

**Overall churn rate: 26.5%** — 1,869 of 7,043 customers. This is the baseline every figure below
should be read against.

<img width="580" height="460" alt="01-churn-distribution" src="https://github.com/user-attachments/assets/cec97b70-7244-468e-92ca-8f9c3d51b6c9" />

**Highest churn segments.** Tenure under 12 months 48.3%, month-to-month contracts 42.7%, fiber
optic 41.9%, no online security 41.8%, senior citizens 41.7%, no tech support 41.6%, paperless
billing 33.6%. These populations overlap heavily, so the rates are marginal, not independent effects.

<img width="2375" height="1941" alt="02-churn-by-segment" src="https://github.com/user-attachments/assets/16107693-17e5-4c7a-be17-40b3cf5f8f19" />

**Revenue concentration.** Churners are 26.5% of customers but 30.5% of monthly recurring revenue —
$139,131 of $456,117. Month-to-month contracts account for $120,847 of that, 87% of the revenue
lost to churn.

<img width="1363" height="460" alt="03-revenue-at-risk" src="https://github.com/user-attachments/assets/f7419775-460c-4234-9797-591c29f3042c" />

**The fiber optic paradox.** Fiber customers pay $91.50 a month on average against $58.10 for DSL, yet
churn 41.9% against 19.0%. They also raise 2.8 times as many technical support tickets (0.70 vs 0.25
per customer). The pattern points at service experience rather than price sensitivity.

<img width="1540" height="460" alt="05-fiber-optic-paradox" src="https://github.com/user-attachments/assets/071e68aa-ff09-481a-9db8-ddd95ecd53df" />

**Bundling effect.** Among customers who buy internet, churn falls monotonically with add-on count:
52.2% with none (n=693), 45.8% with one, 35.8% with two, 27.4% with three, 22.3% with four, 12.4%
with five, 5.3% with all six (n=284). Separately, the 1,526 customers with no internet at all churn
at just 7.4% — the stickiest group in the base, but that reflects a minimal phone-only relationship,
not bundling.

<!-- Chart pending regeneration after the section 10 rewrite (add-on count on internet customers,
     phone-only reported separately). Superseded image, do not re-embed as-is:
     https://github.com/user-attachments/assets/f0ace5b1-0881-42d5-aec1-f37ac76a8832 -->

**Support tickets — technical only.** Administrative tickets carry no churn signal: 27.1% at zero
tickets and 20.6–26.5% across every volume from one to five, with customers raising at least one
admin ticket churning at 24.0%, *below* the 26.5% baseline. Technical tickets are the opposite. Churn
runs 19.7% at zero, jumps to 65.6% at the **first** ticket, and reaches 87.6% at six or more (n=113).
The break is the first tech ticket, and it survives controlling for tenure. Among customers at 25–48 months, 7.1% churn with no tech
ticket against 74.8% with at least one; at 49–72 months, 1.0% against 52.2%. The gap is present in
every tenure band.

<!-- Chart pending regeneration after the section 12 rewrite (admin vs tech tickets side by side,
     shared y-axis). Superseded image, do not re-embed as-is:
     https://github.com/user-attachments/assets/5fe8ee1b-de05-4389-a47f-00aa9e958a80 -->

**High-value churners.** Within the top quintile of monthly charges (threshold $94.25), 467 customers
have already churned — 25.0% of all churners and $47,301 in lost monthly revenue. The group is
75.4% month-to-month, 70.9% without tech support, and 100% fiber. The fiber share is mechanical
rather than behavioural: a $94.25 monthly charge is effectively only reachable on a fiber plan — 1,414 of the 1,415
customers above the threshold are on fiber, and the single exception is a DSL customer at $94.80.

<img width="1540" height="460" alt="07-high-value-churners" src="https://github.com/user-attachments/assets/7e84a655-7500-407f-87b9-7a32f2006b66" />

**Risk scoring.** Six equally weighted flags — month-to-month, fiber, no online security, no tech
support, tenure under 12 months, any technical ticket — track actual churn closely: 0.9% at a score
of zero, 22.9% at three, 46.5% at four, 77.0% at five, 100% at six (n=47). At a threshold of four the
score flags 32% of the base, of whom 60.7% churned, capturing 74.2% of all churners. Applied to
still-active customers it surfaces **898 accounts worth $68,373 in monthly recurring revenue**
at score 4+, or a tighter **226 accounts / $18,084** at score 5+. The score is fitted in-sample; see
Limitations.

<!-- Chart pending regeneration after the section 14 rewrite (six flags, SeniorCitizen removed,
     tech-ticket flag at >= 1). Superseded image, do not re-embed as-is:
     https://github.com/user-attachments/assets/d37cd287-29a9-4b31-ac7c-cdbb648e1035 -->

---

## 🚀 Strategies

**Convert month-to-month contracts first.** Contract length carries both the largest churn gap and
almost all the revenue at risk — $120,847 of the $139,131 lost monthly. A modest discount or an added
service in exchange for a one- or two-year commitment is the highest-leverage single move available.

**Trigger retention on the first technical ticket.** Churn triples between zero and one tech ticket,
and the rate is then flat through five, so there is no reason to wait for escalation — watching for a
third or later ticket adds no information. Administrative tickets should be excluded from any
trigger; they do not predict churn.

**Escalate hardest on new customers with a tech ticket.** The ticket signal is sharpest in the
newest cohort, not weakest: under 12 months, churn runs 46.3% with no tech ticket and 98.7% with at
least one (n=79). The group is small, so it will not move the aggregate on its own, but it is the
highest-conviction list in the base. Everyone else under 12 months still needs a proactive onboarding
play, since 46.3% churn without ever raising a ticket.

**Audit fiber service quality.** Fiber churn travels with a 2.8 times technical ticket rate rather
than with price, which makes network and installation quality the hypothesis worth testing — ticket
volume is a symptom, and this analysis cannot name the cause.

**Push internet add-on adoption.** Add-on count is the cleanest monotonic retention lever in the
data, from 52.2% churn at zero add-ons to 5.3% at six. Security and support attach rates are the
place to start, since both also appear independently among the high churn segments.

**Work the flagged list.** Start with the 226 active customers at score 5+ ($18,084 MRR) as a
measurable pilot, then extend to the 898 at score 4+ ($68,373 MRR) if the intervention pays back.
Price the play before scaling: flagged MRR × retention uplift × accept rate, minus offer cost.

---

## ⚠️ Limitations

**Undocumented ticket definitions.** `numAdminTickets` and `numTechTickets` arrive with no
observation window. If they are lifetime counts, they are confounded with tenure and the ticket
thresholds are biased toward long-tenured customers; if they are recent-window counts, the thresholds
hold as stated. Mean tickets rise only mildly with tenure (0.56 at 0–6 months vs 1.26 at 49–72
months, r = 0.15), which rules out pure lifetime accumulation but does not identify the window. The
support findings should be read as conditional on assumption 3 above. `numAdminTickets` is treated as
non-informative rather than a weak signal, on the evidence that it is flat against churn and
near-uniformly distributed over 1–5.

**Cross-sectional snapshot, no time dimension.** Without event dates there is no way to confirm that
a flagged behaviour preceded the churn decision, so "early warning" is an inference from ordering,
not a demonstrated lead time. Nothing here supports a claim about when to intervene in calendar terms.

**Correlation, not causation.** Fiber, month-to-month and short tenure are heavily overlapping
populations that this analysis does not separate. A logistic regression or stratified crosstab is
required before attributing churn to any single attribute.

**In-sample risk score.** The flags were chosen by inspecting churn in this dataset and validated on
the same dataset, so the 0.9% to 77% gradient is fitted, not predictive performance. There is no
train/test split and no baseline model for comparison. Expect meaningful degradation out of sample.

**"Revenue at risk" is MRR, not loss.** The $139,131 figure is the recurring revenue attached to
customers already recorded as churned. It assumes no reactivation, no downgrade path and no cost to
serve. It is a sizing device, not a P&L number, and should not be annualised.

**Structural nulls encoded as levels.** "No internet service" and "No phone service" are
not-applicable markers, not "No" answers. Add-on counts therefore exclude the 1,526 phone-only
customers rather than scoring them as zero; treating them as zero produces a non-monotonic bundling
curve that mixes phone-only customers (7.4% churn) with internet customers who hold no add-ons
(51.6% churn).

**Age excluded from targeting.** `SeniorCitizen` correlates with churn (41.7%) but was dropped from
the risk score: age is a protected characteristic in most markets, so using it to select customers
for offers carries discrimination risk. Removing it also improved the score's separation, so nothing
analytical was sacrificed.

**Not modelled.** No outlier treatment, no interaction terms, no cost-to-serve or margin data, no
geography, no competitor or pricing context, and no churn-reason field.

---

## ➡️ Next Steps

1. **Get the ticket definitions.** One question to the data owner — what window, what counts as a
   ticket — either confirms the support findings or invalidates their thresholds. Highest-value
   unknown in the project.
2. **Disentangle the overlapping drivers** with a logistic regression on contract, internet type,
   tenure, add-ons and tech tickets, reporting coefficients rather than marginal rates.
3. **Validate the risk score out of sample** with a holdout, and benchmark it against a
   month-to-month-only rule to prove the extra flags earn their complexity.
4. **Price the intervention** so the flagged list carries a business case rather than a headcount.
5. **Instrument the fiber quality hypothesis** with network and installation data.

---

## 📄 Licence & Attribution

**Code.** The analysis in `churn_eda_v2.ipynb` is released under the MIT Licence — see `LICENSE`.

**Data.** `Data/02 Churn-Dataset.xlsx` is not covered by that licence. The underlying IBM Telco Customer
Churn dataset is distributed by IBM as sample data for learning and demonstration purposes; the
Kaggle mirror lists its terms as "Data files © Original Authors". The copy in this repository was
provided to programme participants for the exercise and is included only so the notebook reproduces.
If you intend to reuse the data, take it from one of the sources linked in [`Data/README.md`](Data/README.md), under their terms.

**Trademarks.** PwC and the PwC logo are trademarks of PricewaterhouseCoopers. They appear here
because the case study was produced within PwC's virtual experience programme; this repository is
not affiliated with, endorsed by, or reviewed by PwC.

**Attribution.** Analysis and write-up by Chloe Truong.
