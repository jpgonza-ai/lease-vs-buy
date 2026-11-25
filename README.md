# Lease vs. Buy Calculator

A simple, professional calculator that compares **leasing** an asset with **buying** it (with a loan or in cash), for **four asset types** (equipment, vehicles, technology, real estate) in **two countries** (United States and Mexico). It shows monthly payments, total cash out, the NPV of each option before and after tax, the lease's implied interest rate, a plain-English verdict and a year-by-year cash flow table.

**Live page:** https://jpgonza-ai.github.io/lease-vs-buy/

It is a single `index.html` file: plain HTML, CSS and JavaScript, with no frameworks, no build step and no external libraries. It works in any browser, including on phones, and runs entirely on your device (nothing is sent anywhere).

## How to use it

1. Open the live page (or download `index.html` and double-click it).
2. Choose a **country** (United States / Mexico) and an **asset type** (Equipment / Vehicle / Technology / Real estate). Each choice loads a set of editable example values.
3. Adjust the inputs on the left:
   - **Asset:** price, useful life, depreciation rate, and expected resale value at the end of the lease term. For real estate, enter the land value % and annual appreciation % instead.
   - **Option A, Lease (or Rent for real estate):** term (months), monthly payment, upfront payment, payment timing (advance or arrears), and the end-of-term option (return, or buy out for a set amount).
   - **Option B, Buy:** down payment %, loan APR and term for the typical financing (equipment loan, auto loan, technology loan or mortgage), or pay cash.
   - **Assumptions:** discount rate (cost of capital) and the country's tax settings.
4. Results update instantly. **Reset to example values** reloads the defaults for the current country and asset.

## What it calculates

| Output | Meaning |
|---|---|
| Monthly payment | Lease/rent payment (as entered) and the loan payment (calculated). |
| Total cash out | Every amount paid over the lease term: upfront, payments, buyout, any loan balance still owed at the end, and non-creditable IVA. |
| NPV of cost | All cash flows converted to today's money at your discount rate. Lower is better. Shown pre-tax and after tax. |
| Implied rate | The interest rate built into the lease, comparable with a loan APR. Also shows the lease rate factor (monthly payment ÷ price). |
| Verdict | Which option is cheaper, by how much in today's money, and why. Uses the after-tax NPV when a tax rate is entered, otherwise pre-tax. |
| Year-by-year table | Net cash flows by year for both options, pre-tax and after tax. |

## Asset types

Each asset type loads its own example price, useful life, depreciation rate, residual value, lease term and financing type.

| Asset type | Financing | Lease term | Residual value | US depreciation | Mexico depreciation |
|---|---|---|---|---|---|
| Equipment | Equipment loan | 36 months | 40% of price | 7-year life → 14.29%/yr | 10%/yr (LISR Art. 35, varies by industry) |
| Vehicle | Auto loan | 36 months | 50% of price | 5-year life → 20%/yr | 25%/yr (LISR Art. 34) |
| Technology | Technology loan | 36 months | 10% of price | 5-year life → 20%/yr | 30%/yr (LISR Art. 34) |
| Real estate | Mortgage (20 years) | 60 months (rent) | Price grown by appreciation | 39-year life → 2.56%/yr, building only | 5%/yr (LISR Art. 34), building only |

**Real estate works differently:**
- **Lease means renting the property.** "Return" means moving out at the end of the term.
- **Buy means a mortgage.** If the mortgage is longer than the rental term, the remaining balance is paid off at the end of the term so both options cover the same period.
- **Land is not depreciated.** The *land value %* is excluded from the depreciable basis; only the building is depreciated.
- **The value can grow.** The end-of-term value is `price × (1 + appreciation)^(months / 12)`, and that is what you keep if you buy.

The example lease payments are set so the lease's implied rate is slightly above the example loan APR (+0.3 to +1.0 points). That's a realistic starting point, not a quote.

## Countries

### United States (USD)
- **Federal corporate tax: 21%** (Internal Revenue Code §11(b)).
- **State tax rate:** enter your own state's corporate rate (defaults to 0%). State income tax is generally deductible for federal purposes, so the after-tax view uses the combined rate:
  `combined = federal + state × (1 − federal)`. For example, 21% federal + 6% state = 25.74%.
- **Depreciation:** straight-line at the rate shown (100 ÷ useful life; changing the useful life updates it). MACRS, bonus depreciation and Section 179 are not modeled.

### Mexico (MXN)
- **Corporate income tax (ISR): 30%** (LISR Art. 9).
- **Depreciation rate per asset type:** the maximum rate depends on the type of asset under the Mexican income tax law (LISR Arts. 34–35). Confirm the rate for your asset.
- **IVA 16%** (LIVA Art. 1) on lease payments, upfront fees, loan interest, the purchase price and any buyout. On real estate it applies to the building only (land is exempt), and the property is assumed to be commercial.
  - **"IVA is creditable (recoverable)" on (default):** IVA is recovered, so it does not change the comparison and is left out.
  - **Off:** IVA is counted as a cost. Non-creditable IVA is treated as deductible for ISR together with the expense it relates to: immediately for rent and interest, and through depreciation (added to the tax basis) for the purchase price or buyout. IVA on the purchase price is paid in cash at signing (not financed).
- **Not modeled:** the LISR caps on deductible car cost and daily car lease deductions, PTU (employee profit sharing), and inflation adjustment of depreciation.

## Example defaults and sources

The defaults are **examples, not live market rates.** Enter your current rates. The page shows "Defaults as of November 25, 2026" with these sources:

| Default | United States | Mexico |
|---|---|---|
| Corporate tax | 21% federal, IRC §11(b); state rate entered by the user | ISR 30%, LISR Art. 9 |
| VAT | n/a | IVA 16%, LIVA Art. 1 |
| Loan APRs | Equipment 8.5%, auto 8.0%, technology 9.0%: example spreads over the bank prime rate of 7.00% (Federal Reserve H.15, Nov 18, 2026). Mortgage 7.5%: example commercial rate near the Freddie Mac PMMS 30-year fixed average of 7.28% (Nov 19, 2026), which is a residential benchmark | Equipment 13%, auto 14%, technology 15%, mortgage 12%: example spreads over Banxico's target rate of 6.25% (announcement of Nov 5, 2026) |
| Discount rate | 9% (illustrative) | 14% (illustrative) |
| Depreciation | IRS Publication 946 recovery periods, applied straight-line | LISR Arts. 34–35 |
| Residuals, appreciation, land % | Illustrative | Illustrative |

## Formulas

All math is in pure, commented functions at the top of the `<script>` section of `index.html`.

**Loan payment (amortization).** With monthly rate `r = APR / 12`, principal `P` and `n` months:

```
PMT = P × r / (1 − (1 + r)^−n)        (PMT = P / n when r = 0)
```

Each month: interest = balance × r, principal = PMT − interest.

**NPV.** The annual discount rate `R` is converted to an equivalent monthly rate `d = (1 + R)^(1/12) − 1`, then:

```
NPV = Σ CF_t / (1 + d)^t     for t = 0 … n months
```

**Implied lease rate.** The monthly rate `i` that makes the lessor's cash flows break even (IVA excluded):

```
Price = Upfront + Σ Rent_t / (1 + i)^t + EndValue / (1 + i)^n
```

`EndValue` is the buyout amount if you buy it out, or the expected residual value if you return the asset (the lessor gets it back). Solved numerically by bisection and reported as `i × 12` so it is comparable with a loan APR.

**Depreciation.** `monthly depreciation = depreciable basis × rate / 12`, where the depreciable basis is `price × (1 − land %)` plus any non-creditable IVA on it. It stops once the basis is fully depreciated.

**Tax on the end value.** `tax = tax rate × (residual value − tax book value)`, where book value = price + non-creditable IVA − accumulated depreciation (land stays on the books at cost).

## Assumptions

- **Same horizon for both options:** the lease term. If the loan is longer than the lease, the remaining loan balance is paid off at the end of the lease term.
- **End value:** when you own the asset at the end (buy, or lease with buyout), it is counted as money coming back to you at its expected value, as if sold, and any gain over the tax book value is taxed.
- **Timing:** lease payments are in advance or arrears (your choice); loan payments are monthly in arrears; upfront amounts, down payments and IVA on the price are paid on signing day.
- **Taxes:** lease/rent payments and upfront fees are deductible when paid (treated as a true/operating lease). When buying, loan interest and depreciation are deductible. Tax effects are applied in the same month as the cash flow, and it's assumed the company has enough taxable income to use them. Set the tax rates to 0 to ignore taxes.
- **Discount rate:** the same rate is used for the pre-tax and after-tax views. For a textbook after-tax analysis, you can enter your after-tax cost of debt.
- **Not modeled:** maintenance, insurance, property taxes, sales tax (US), closing costs, security deposits, refundable deposits, and accounting treatment (ASC 842 / IFRS 16).

## Publishing with GitHub Pages

In the repository: **Settings → Pages → Build and deployment → Source: Deploy from a branch → Branch: `main`, folder `/ (root)` → Save.** After a minute or two the site is live at the link above.

---

Built with Claude Code · For educational purposes only, not financial advice.
