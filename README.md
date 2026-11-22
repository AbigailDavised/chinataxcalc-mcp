# ChinaTaxCalc MCP Server — China Expat IIT, BIA Allowance, Annual Bonus & GBA 15% Rebate Engine

<p align="center">
  <img src="./assets/logo.svg" alt="ChinaTaxCalc Logo" width="80" height="80" />
</p>

## Overview & Production Architecture

**ChinaTaxCalc** ([https://chinataxcalc.com](https://chinataxcalc.com)) exposes a stateless, zero-authentication **Model Context Protocol (MCP)** server (`streamable-http` at `https://chinataxcalc.com/api/mcp`) backed directly by mainland China Individual Income Tax (IIT) engines, MOF/STA Announcements `[2023] No. 29` (tax-exempt expat BIA housing and international school tuition through 2027) and `[2023] No. 30` (annual one-off bonus separate calculation & cliff dead-zone detection), STA `[2018] No. 56` 12-month cumulative withholding simulations, six municipal social insurance caps, nine Greater Bay Area 15% fiscal rebate hubs, seven international relocation benchmarks (`US-CA, US-NY, US-TX, UK, DE, SG, HK`), the 6-year worldwide tax 31-day reset rule, and SAFE Circular `[2020] No. 14` salary repatriation bank checklists. All 8 tools are public and annotated `readOnlyHint: true` with no API keys required.

## Available MCP Tools (8 Read-Only Tools)

| Tool Name | Purpose & Deterministic Engine | Key Parameters | Safety |
| :--- | :--- | :--- | :---: |
| `calculate_china_expat_iit_and_net_salary` | Compute annual and monthly net take-home cash, marginal brackets, employer costs, and tax savings from eight Fapiao-backed BIA allowances across  | `grossAmount`*, `incomePeriod`, `currency`, `cityId` (+4 more) | `readOnly` |
| `simulate_cumulative_monthly_withholding` | Simulate January-to-December paycheck variance under STA Announcement 2018 No. 56, highlighting bracket jump months and lumpy school tuition spik | `monthlyGrossRMB`*, `monthlySSRMB`, `monthlyBIARMB`, `lumpyTuitionMode` (+1 more) | `readOnly` |
| `compare_annual_bonus_separate_vs_combined_tax` | Contrast MOF/STA Announcement 30 separate bonus taxation against combined comprehensive filing and warn about statutory bracket cliff traps. | `annualTaxableSalaryRMB`*, `annualBonusRMB`* | `readOnly` |
| `analyze_landlord_fapiao_surcharge_roi` | Evaluate whether paying a landlord surcharge to obtain official housing VAT invoices generates positive after-tax savings at your marginal rate. | `monthlyRentRMB`*, `surchargeRatePct`*, `marginalRate`* | `readOnly` |
| `calculate_gba_15pct_talent_tax_subsidy` | Quantify municipal fiscal refunds across nine Greater Bay Area hubs like Shenzhen Qianhai and Guangzhou Nansha where effective burden caps at 15  | `actualIITPaidRMB`*, `taxableIncomeRMB`*, `gbaCityId` | `readOnly` |
| `compare_cross_border_relocation_net_pay` | Benchmark equivalent USD compensation across seven global financial hubs against Shanghai expat net pay with cost-of-living index comparisons. | `grossUSD`*, `comparisonHubs`, `chinaCityId`, `chinaHousingAllowanceUSD` | `readOnly` |
| `diagnose_six_year_worldwide_tax_rule` | Track consecutive 183-day residency years and verify whether a continuous 30+ day absence resets non-domiciled offshore income protection. | `records`* | `readOnly` |
| `generate_safe_salary_repatriation_checklist` | Assemble the six mandatory counter verification items, Tax App registration tips, and bilingual SAFE quota exemption text for wiring RMB abroad. | `remitAmountRMB`, `targetCurrency` | `readOnly` |

## Live JSON-RPC 2.0 Verification Sample

Request (`POST https://chinataxcalc.com/api/mcp`):

```bash
curl -s -X POST https://chinataxcalc.com/api/mcp \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"calculate_china_expat_iit_and_net_salary","arguments":{"grossAmount":45000,"incomePeriod":"monthly","currency":"RMB","cityId":"shanghai","socialSecurityMode":"exempt","annualBonus":90000,"housingRental":12000}}}'
```

Verified Production Response:

```json
{
  "sourceEngine": "src/utils/taxCalculations.ts (calculateExpatIIT)",
  "cityProfile": {
    "id": "shanghai",
    "name": "Shanghai",
    "minSocialBaseRMB": 7310,
    "maxSocialBaseRMB": 36549,
    "notes": "Expat Social Security exemption is historically supported in Shanghai (Hu Ren She Han [2021] No. 272). Foreign hires may mutually agree with employers to opt out."
  },
  "exchangeRateToRMB": 1,
  "standardAnnualDeductionRMB": 60000,
  "summary": {
    "currency": "RMB",
    "annualNetPay": 571440,
    "monthlyNetPay": 47620,
    "annualIIT": 58560,
    "monthlyIIT": 4880,
    "annualSocialSecurityEmployee": 0,
    "effectiveTaxRatePct": 9.3,
    "marginalTaxBracketPct": 25,
    "netTakeHomePct": 90.7,
    "taxSavingsFromBIA": 39000,
    "annualEmployerCost": 630000
  },
  "rmbBreakdown": {
    "totalAnnualGrossRMB": 630000,
    "totalAnnualBIARMB": 144000,
    "annualTaxableIncomeRMB": 336000,
    "annualComprehensiveIITRMB": 52080,
    "annualBonusIITRMB": 6480,
    "totalAnnualIITRMB": 58560,
    "totalAnnualNetPayRMB": 571440,
    "taxSavingsFromBIARMB": 39000
  },
  "calculatorUrl": "https://chinataxcalc.com"
}
```

## Quick Client Configuration (Claude Desktop / Cursor / Windsurf / Gemini CLI / Hermes)

```json
{
  "mcpServers": {
    "chinataxcalc": {
      "type": "http",
      "url": "https://chinataxcalc.com/api/mcp"
    }
  }
}
```

Or run directly from any Node.js terminal via the pinned official CLI:

```bash
npx -y chinataxcalc-cli@1.0.0 tools
npx -y chinataxcalc-cli@1.0.0 demo
```

## Statutory Compliance & Tax Reference Matrices

### China Comprehensive Income 7-Tier Progressive IIT Brackets (Standard Deduction: ¥60,000/yr)

| Level | Annual Taxable Income (RMB) | Statutory Marginal Rate | Quick Deduction (RMB) | Statutory Basis |
| :---: | :--- | :---: | :---: | :--- |
| **1** | `¥0 – ¥36,000` | **`3%`** | `¥0` | PRC IIT Law (Comprehensive Income) |
| **2** | `¥36,001 – ¥144,000` | **`10%`** | `¥2,520` | PRC IIT Law / STA [2018] No. 56 |
| **3** | `¥144,001 – ¥300,000` | **`20%`** | `¥16,920` | PRC IIT Law / STA [2018] No. 56 |
| **4** | `¥300,001 – ¥420,000` | **`25%`** | `¥31,920` | PRC IIT Law / STA [2018] No. 56 |
| **5** | `¥420,001 – ¥660,000` | **`30%`** | `¥52,920` | PRC IIT Law / STA [2018] No. 56 |
| **6** | `¥660,001 – ¥960,000` | **`35%`** | `¥85,920` | PRC IIT Law / STA [2018] No. 56 |
| **7** | `Exceeding ¥960,000` | **`45%`** | `¥181,920` | PRC IIT Law / Top Marginal Bracket |

### Six-City Expat Social Insurance Base Caps & Policy Matrix

| Municipality | Social Average Wage (RMB/mo) | Contribution Base Floor | Contribution Base Ceiling (3x) | Expat Participation & Regional Tax Rules |
| :--- | :---: | :---: | :---: | :--- |
| **Shanghai** | `¥12,183` | `¥7,310` | `¥36,549` | Historical opt-out flexibility under Hu Ren She Han [2021] No. 272 |
| **Beijing** | `¥11,761` | `¥6,326` | `¥35,283` | Strictly mandatory unless covered by bilateral totalization treaty |
| **Shenzhen** | `¥8,807` | `¥4,404` | `¥26,421` | Qianhai / GBA 15% effective IIT fiscal subsidy (capped RMB 5M/yr) |
| **Guangzhou** | `¥8,807` | `¥5,284` | `¥26,421` | Nansha / Huangpu 15% IIT ceiling rebate for verified overseas talent |
| **Hangzhou** | `¥8,023` | `¥4,814` | `¥24,069` | Mandatory municipal contribution rules up to 300% cap |
| **Chengdu** | `¥7,356` | `¥4,414` | `¥22,068` | Mandatory social insurance with Western China talent incentives |

### Eight Statutory Expat Tax-Exempt BIA Allowances (MOF/STA Announcement [2023] No. 29 · Through Dec 31, 2027)

| Allowance Category | Documentation Requirement | Payroll Treatment & Key Caution |
| :--- | :--- | :--- |
| **Housing Rental** | Official tax bureau VAT rental Fapiao | Direct tax-base deduction; analyze landlord Fapiao fee ROI vs marginal tax rate |
| **Children Education** | Accredited school tuition invoice inside China | Must match non-cash direct payment or genuine Fapiao reimbursement |
| **Language Training** | Accredited Mandarin school receipt | Eligible for bona fide language instruction for the foreign assignee |
| **Home Leave Flights** | Airline itinerary + Fapiao (up to 2 round trips/yr) | Covers economy/business fare between work city in China and home country |
| **Meal & Laundry Allowances** | Corporate receipts / legitimate VAT Fapiao | Must be reasonable and non-cash fixed subsidy |
| **Relocation & Travel Per Diem** | Moving invoices / business travel policy receipts | Documented arrival, departure, or business travel costs remain non-taxable |

## Official Web Calculators & Regulatory Guides

- **[ChinaTaxCalc Official Portal — China Expat IIT & Net Salary Calculator](https://chinataxcalc.com)** — `https://chinataxcalc.com`
- **[Expat Tax-Exempt BIA Housing & Education Allowances (No. 29)](https://chinataxcalc.com/expat-allowances-bia)** — `https://chinataxcalc.com/expat-allowances-bia`
- **[Annual One-Off Bonus Tax Cliff & Separate Filing (No. 30)](https://chinataxcalc.com/annual-bonus-tax)** — `https://chinataxcalc.com/annual-bonus-tax`
- **[Shanghai Expat IIT & Social Security Exemption Simulator](https://chinataxcalc.com/shanghai-expat-tax)** — `https://chinataxcalc.com/shanghai-expat-tax`
- **[2026/2027 China 7-Bracket Individual Income Tax Rates Reference](https://chinataxcalc.com/tax-brackets)** — `https://chinataxcalc.com/tax-brackets`
- **[Cross-Border Relocation Tax Benchmark (China vs US/UK/DE/SG/HK)](https://chinataxcalc.com/relocation)** — `https://chinataxcalc.com/relocation`
- **[Greater Bay Area 15% Overseas Talent Fiscal Subsidy Calculator](https://chinataxcalc.com/gba-subsidy)** — `https://chinataxcalc.com/gba-subsidy`
- **[China 6-Year Worldwide Tax Exemption & 31-Day Reset Diagnostic](https://chinataxcalc.com/six-year-rule)** — `https://chinataxcalc.com/six-year-rule`
- **[Foreign Expat Salary Repatriation & SAFE Circular [2020] No. 14 Guide](https://chinataxcalc.com/repatriation)** — `https://chinataxcalc.com/repatriation`

## License

MIT License — maintained by **[ChinaTaxCalc](https://chinataxcalc.com)**.
