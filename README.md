# Lease vs. Buy Calculator

A simple, professional calculator that compares **leasing** a piece of equipment with **buying** it (with a loan or in cash). It shows monthly payments, total cash out, the NPV of each option before and after tax, the lease's implied interest rate, a plain-English verdict and a year-by-year cash flow table.

**Live page:** https://jpgonza-ai.github.io/lease-vs-buy/

It is a single `index.html` file: plain HTML, CSS and JavaScript, with no frameworks, no build step and no external libraries. It works in any browser, including on phones, and runs entirely on your device (nothing is sent anywhere).

## How to use it

1. Open the live page (or download `index.html` and double-click it).
2. Fill in the inputs on the left. Example values are pre-filled.
   - **Equipment:** price, useful life (years) and expected resale value at the end of the lease term.
   - **Option A, Lease:** term (months), monthly payment, upfront payment, whether payments are in advance or in arrears, and the end-of-lease option (return, or buy out for a set amount).
   - **Option B, Buy:** down payment %, loan APR and loan term, or pay cash.
   - **Assumptions:** discount rate (cost of capital) and an optional corporate tax rate.
3. Results update instantly on the right. Click **Reset to example values** to start over.

## What it calculates

| Output | Meaning |
|---|---|
| Monthly payment | Lease payment (as entered) and the loan payment (calculated). |
| Total cash out | Every dollar paid over the lease term: upfront, payments, buyout, and any loan balance still owed at the end. |
| NPV of cost | All cash flows converted to today's dollars at your discount rate. Lower is better. Shown pre-tax and after tax. |
| Implied rate | The interest rate built into the lease, comparable with a loan APR. Also shows the lease rate factor (monthly payment ÷ price). |
| Verdict | Which option is cheaper, by how much in today's dollars, and why. Uses the after-tax NPV when a tax rate is entered, otherwise pre-tax. |
| Year-by-year table | Net cash flows by year for both options, pre-tax and after tax. |

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

**Implied lease rate.** The monthly rate `i` that makes the lessor's cash flows break even:

```
Price = Upfront + Σ Rent_t / (1 + i)^t + EndValue / (1 + i)^n
```

`EndValue` is the buyout amount if you buy it out, or the expected residual value if you return the equipment (the lessor gets it back). Solved numerically by bisection and reported as `i × 12` so it is comparable with a loan APR.

## Assumptions

- **Same horizon for both options:** the lease term. If the loan is longer than the lease, the remaining loan balance is paid off at the end of the lease term.
- **Residual value:** when you own the equipment at the end (buy, or lease with buyout), it is counted as money coming back to you at its expected resale value, as if sold.
- **Timing:** lease payments are in advance or arrears (your choice); loan payments are monthly in arrears; upfront amounts and down payments are paid on signing day.
- **Taxes (optional):** lease payments and upfront fees are deductible when paid (treated as a true/operating lease). When buying, loan interest and straight-line depreciation (price ÷ useful life, to zero) are deductible; when the equipment is sold at the end, tax is paid on the gain over its tax book value (or saved on a loss). A lease buyout price becomes the tax basis. Tax effects are applied in the same month as the cash flow, and it's assumed the company has enough taxable income to use them.
- **Discount rate:** the same rate is used for the pre-tax and after-tax views. For a textbook after-tax analysis, you can enter your after-tax cost of debt.
- Not modeled: maintenance, insurance, sales tax, bonus depreciation/Section 179, security deposit refunds, or accounting treatment (ASC 842 / IFRS 16).

## Publishing with GitHub Pages

In the repository: **Settings → Pages → Build and deployment → Source: Deploy from a branch → Branch: `main`, folder `/ (root)` → Save.** After a minute or two the site is live at the link above.

---

Built with Claude Code · For educational purposes only, not financial advice.
