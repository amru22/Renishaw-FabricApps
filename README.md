# Renishaw Fabric Apps Lab

This repository contains customer-ready Microsoft Fabric Apps lab documentation for synthetic finance scenarios. The labs support a 30-minute Renishaw finance session with 10-15 minutes of presentation and 15-20 minutes of live demo.

## Scenario

Renishaw's finance team wants lightweight internal finance applications that help finance business partners review cost centre spend, invoice risk, forecast variance, working-capital exposure, and review actions without waiting for a full bespoke application project.

The labs show two Fabric Apps patterns built with Rayfin:

1. A direct-SQL app that reads finance transactions and budget targets from the Fabric Apps managed SQL database.
1. A hybrid finance app where SQL source data feeds a semantic model, while Rayfin stores operational review actions.

## Repository structure

```text
Renishaw-FabricApps/
  README.md
  data/
    finance_app_budget_targets.csv
    finance_app_transactions.csv
    finance_app_semantic_model/
      dim_category.csv
      dim_cost_centre.csv
      dim_date.csv
      dim_supplier.csv
      fact_finance_transaction.csv
  labs/
    build-renishaw-finance-control-tower.md
    build-finance-app.md
```

## Lab files

| File | Purpose |
| --- | --- |
| `labs/build-renishaw-finance-control-tower.md` | Build lab for the direct-SQL Renishaw Finance Control Tower app. |
| `labs/build-finance-app.md` | Build lab for the semantic-model-backed Finance App with Rayfin workflow write-back. |
| `data/finance_app_transactions.csv` | Synthetic finance transaction data for invoice, accrual, forecast, and capex examples. |
| `data/finance_app_budget_targets.csv` | Synthetic monthly budget and risk tolerance data by cost centre. |
| `data/finance_app_semantic_model/*.csv` | Star-schema CSV source files for the Finance App semantic-model lab. |

All data is synthetic and for training/demo use only.
