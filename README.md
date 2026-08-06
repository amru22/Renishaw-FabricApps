# Renishaw Fabric Apps Lab

This repository contains a short, customer-ready Microsoft Fabric Apps lab for a synthetic finance scenario. It is designed for a 20-25 minute guided walkthrough where the app is built in advance, then demoed slowly with a clear business narrative.

## Scenario

Renishaw's finance team wants a lightweight internal finance control app that helps finance business partners review cost centre spend, invoice risk, forecast variance, and cash impact without waiting for a full bespoke application project.

The lab shows how Fabric Apps can turn TypeScript data models into a managed app backend with GraphQL APIs, Fabric authentication, SQL database storage, and static hosting.

## Repository structure

```text
Renishaw-FabricApps/
  README.md
  data/
    finance_app_budget_targets.csv
    finance_app_transactions.csv
  labs/
    01-build-finance-fabric-app.md
    02-demo-script-finance-fabric-app.md
```

## Lab files

| File | Purpose |
| --- | --- |
| `labs/01-build-finance-fabric-app.md` | Prescriptive build guide for preparing the Fabric App before the customer demo. |
| `labs/02-demo-script-finance-fabric-app.md` | 20-25 minute customer demo script with exact flow, talk track, and feature callouts. |
| `data/finance_app_transactions.csv` | Synthetic finance transaction data for invoice, accrual, forecast, and capex examples. |
| `data/finance_app_budget_targets.csv` | Synthetic monthly budget and risk tolerance data by cost centre. |

All data is synthetic and for training/demo use only.

