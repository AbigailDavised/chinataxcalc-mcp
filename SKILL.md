---
name: chinataxcalc
description: "Calculate China expat Individual Income Tax (IIT) and net take-home cash across 6 cities, model 8 Fapiao-backed BIA exemptions, detect MOF/STA No. 30 annual bonus cliffs, compute GBA 15% subsidies, and verify 6-year worldwide tax rules via ChinaTaxCalc's stateless MCP server."
homepage: https://chinataxcalc.com
version: 1.0.0
license: MIT
compatibility: "Works with Gemini CLI, Hermes Agent, Claude Code, Cursor, Windsurf, and any MCP client supporting Streamable HTTP."
metadata:
  homepage: "https://chinataxcalc.com"
  mcp_endpoint: "https://chinataxcalc.com/api/mcp"
  version: "1.0.0"
  hermes:
    category: finance
    tags: [china-tax, expat-iit, salary-calculator, bia-allowances, gba-subsidy, repatriation]
---

# ChinaTaxCalc — Stateless China Expat IIT & Net Salary MCP Skill

Connect any MCP-compatible AI agent to ChinaTaxCalc's public read-only Streamable HTTP server at `https://chinataxcalc.com/api/mcp`. Zero sign-in or API keys required.

## Endpoint Configuration

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

## Direct JSON-RPC 2.0 Verification

```bash
curl -s -X POST https://chinataxcalc.com/api/mcp \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"calculate_china_expat_iit_and_net_salary","arguments":{"grossAmount":45000,"incomePeriod":"monthly","currency":"RMB","cityId":"shanghai","socialSecurityMode":"exempt","housingRental":12000}}}'
```

## Tools Inventory (8 Read-Only Tools)

- `calculate_china_expat_iit_and_net_salary`: Compute annual/monthly net cash, marginal tax brackets, employer costs, and BIA tax savings across 6 Chinese cities.
- `simulate_cumulative_monthly_withholding`: Simulate Jan-Dec payroll withholding under STA Announcement 2018 No. 56 including bracket jumps and tuition timing.
- `compare_annual_bonus_separate_vs_combined_tax`: Compare MOF/STA 2023 No. 30 separate bonus calculation vs combined income and flag bonus cliff zones.
- `analyze_landlord_fapiao_surcharge_roi`: Evaluate whether paying a landlord surcharge for an official rental VAT Fapiao produces net after-tax savings.
- `calculate_gba_15pct_talent_tax_subsidy`: Compute Guangdong-Hong Kong-Macao Greater Bay Area 15% fiscal tax rebate (capped at RMB 5M/yr).
- `compare_cross_border_relocation_net_pay`: Benchmark net compensation between China and US-CA, US-NY, US-TX, UK London, Germany, Singapore, and Hong Kong.
- `diagnose_six_year_worldwide_tax_rule`: Check consecutive 183-day residency years and whether a single >30-day trip resets worldwide tax liability.
- `generate_safe_salary_repatriation_checklist`: Return the 6 counter items and bilingual Chinese bank script under SAFE Circular [2020] No. 14.
