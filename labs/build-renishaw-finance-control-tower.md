# Build lab - Renishaw Finance Control Tower

## Lab overview

This lab builds the **Renishaw Finance Control Tower** app: a direct-SQL Fabric App built with Rayfin.

The app reads finance transactions and budget targets from the Fabric Apps managed SQL database through the Rayfin typed data client. It renders a focused finance risk dashboard with KPI cards, a selectable risk queue, transaction detail, and a cost-centre budget summary.

This is the simpler of the two demo apps. Use it to show the shortest path from app-owned SQL data to an interactive Fabric App.

> Presenter note:
> This app is read-only from the end-user UI. The seed script writes demo data into SQL, but the dashboard itself only reads and presents the data.

## Business scenario

Renishaw finance users need a fast way to review a small set of finance control items:

- supplier spend requiring attention
- high-risk invoices or accruals
- capex review items
- due dates and owners
- cost-centre budget position

The scenario is synthetic and illustrative only. Do not imply that the data is real Renishaw finance data.

> Say this:
> "This first app is a deliberately simple finance control tower. It shows how Rayfin can turn managed SQL data into an authenticated operational app inside Fabric without starting from custom infrastructure."

## What the app demonstrates

| Capability | What the app shows |
| --- | --- |
| Fabric App item | The deployed app runs as a Fabric item. |
| Rayfin SDK/CLI | Rayfin provides the app framework, CLI, typed data client, auth integration, and deployment workflow. |
| Managed SQL database | Finance transactions and budget targets are stored in the Fabric Apps SQL data service. |
| Typed API access | The frontend reads data through Rayfin typed `.select()` queries. |
| Finance-focused UI | Users see risk, review status, transaction context, and budget variance. |

## Architecture summary

```text
TypeScript seed data
        |
        v
Rayfin seed script
        |
        v
Fabric Apps managed SQL database
        |
        v
Rayfin typed data client / generated GraphQL API
        |
        v
React finance dashboard
```

The app does not use a Power BI semantic model. Calculations such as total spend, high-risk count, review count, and budget variance are calculated in React from SQL rows.

## Prerequisites

Before starting, confirm:

- You have access to a Fabric workspace on capacity.
- Fabric Apps are enabled for your tenant.
- You have permission to create and edit Fabric App items in the workspace.
- Node.js is installed.
- Docker Desktop is available if required by your local Rayfin workflow.
- You can authenticate with the Rayfin CLI.
- You have the app repository for **Renishaw Finance Control Tower**.

> CHECK BEFORE DELIVERY:
> Confirm the exact local folder for this app. The audit identified it as a separate sibling repository from the `FinanceApp` workspace.

## Required repo files

The app should contain these key files or equivalents:

| File or folder | Purpose |
| --- | --- |
| `rayfin.yml` | Fabric Apps service configuration, auth, SQL data service, and hosting. |
| `data/FinanceTransaction.ts` | Rayfin entity for finance transaction rows. |
| `data/BudgetTarget.ts` | Rayfin entity for cost-centre budget targets. |
| `data/schema.ts` | Registers app entities for the typed data client. |
| `HomePage.tsx` | Main finance dashboard UI. |
| `financeDataService.ts` | Reads finance transactions and budget targets through the Rayfin typed client. |
| `seedFinanceData.ts` | Seeds the SQL database with synthetic demo records. |
| `package.json` | Contains build, dev, seed, and deployment scripts. |

> CHECK BEFORE DELIVERY:
> File locations can differ depending on the Rayfin template. Search for `HomePage.tsx`, `financeDataService.ts`, and `seedFinanceData.ts` if they are not in the project root.

## Required Fabric workspace and capacity assumptions

Use these names for consistency:

| Item | Recommended value |
| --- | --- |
| Fabric workspace | `Renishaw-FabricApps` |
| Fabric App item | `Renishaw Finance Control Tower` |
| App purpose | Direct SQL finance control dashboard |
| Data type | Synthetic finance demo data |

## Required Rayfin/Fabric Apps setup

Open `rayfin.yml` and check:

1. Fabric auth is enabled for deployment.
1. The SQL data service is enabled.
1. Static hosting is enabled.
1. The Fabric workspace and app item IDs are populated after deployment.

> CHECK BEFORE DELIVERY:
> Do not show `.env` values or secrets during the customer session. If environment values are needed locally, keep them out of the presentation.

## Required data setup

The app uses two SQL-backed Rayfin entities.

### `FinanceTransaction`

Represents an operational finance item.

Typical fields:

- `transactionId`
- `transactionType`
- `costCentre`
- `supplier`
- `category`
- `amountGbp`
- `invoiceDate`
- `dueDate`
- `forecastMonth`
- `status`
- `riskLevel`
- `varianceDriver`
- `financeOwner`
- `commentary`

### `BudgetTarget`

Represents cost-centre control targets.

Typical fields:

- `costCentre`
- `financeOwner`
- `monthlyBudgetGbp`
- `quarterlyBudgetGbp`
- `riskToleranceGbp`
- `workingCapitalPriority`

The audited app seed contains four transaction records and four budget target records. This is enough for a short architecture demo, but not enough for analytical depth.

> Presenter note:
> Call this app a "pattern demo" rather than a full finance analysis solution.

## Step-by-step build instructions

### Step 1 - Open the app repository

In a terminal:

```bash
cd "<path to renishaw-finance-control-tower>"
code .
```

Check you are in the correct app by finding these files:

```bash
dir
```

You should see `rayfin.yml`, `package.json`, and the source folders.

> Presenter note:
> Keep this step out of the live customer demo. Do it during preparation.

### Step 2 - Install dependencies

Run:

```bash
npm install
```

Then confirm the project builds:

```bash
npm run build
```

> CHECK BEFORE DELIVERY:
> If `npm run build` is not defined, inspect `package.json` and use the build script provided by the app.

### Step 3 - Review the Rayfin entities

Open:

```text
data/FinanceTransaction.ts
data/BudgetTarget.ts
data/schema.ts
```

Check that:

1. `FinanceTransaction` and `BudgetTarget` exist.
1. Both entities are registered in `schema.ts`.
1. The fields match the finance scenario.
1. The role decorators allow authenticated access.

> Presenter note:
> Do not claim the app has business-role-level security unless you have added it. The audit found authenticated access, not finance-persona RLS.

### Step 4 - Apply or deploy the SQL schema

Run:

```bash
npx rayfin up db apply
```

If this is a first-time deployment, you may instead need:

```bash
npx rayfin up
```

What to check:

- The command completes without schema errors.
- The Fabric App item exists in the Fabric workspace.
- The SQL child service exists under the Fabric App item.

> CHECK BEFORE DELIVERY:
> If `db apply` reports destructive schema changes, stop and inspect the generated changes before using any force option.

### Step 5 - Seed the SQL database

Run the seed script from the app root.

Expected command:

```bash
npm run seed:finance
```

> CHECK BEFORE DELIVERY:
> Confirm the exact script name in `package.json`. The audit identified a `seedFinanceData.ts` script, but the package script name must be verified.

The seed should insert synthetic rows into:

- `FinanceTransaction`
- `BudgetTarget`

What to check:

- The script reports inserted or skipped rows.
- Running it twice does not create duplicate demo records.
- The Fabric App SQL database has rows for the two entities.

### Step 6 - Review the data access service

Open:

```text
financeDataService.ts
```

Check for typed Rayfin data queries similar to:

```typescript
FinanceTransaction.select([...])
  .orderBy({ dueDate: 'asc' })
  .first(1000)
  .execute();

BudgetTarget.select([...])
  .orderBy({ costCentre: 'asc' })
  .first(1000)
  .execute();
```

What this means:

- The app does not write custom GraphQL documents.
- Rayfin generates the API access from the entity model.
- The frontend uses typed data access rather than raw SQL.

> Say this:
> "The finance app is not connecting directly to a database connection string from the browser. It is using the Rayfin app data API generated from the entity model."

### Step 7 - Review the frontend page

Open:

```text
HomePage.tsx
```

Check that the UI includes:

- KPI cards
- Risk Queue
- transaction detail panel
- Cost Centre Summary

Confirm the page calculates:

- total spend
- high-risk item count
- review item count
- spend by cost centre
- variance against monthly budget

> Presenter note:
> These calculations are React calculations over SQL rows, not semantic-model measures.

### Step 8 - Run locally

Run:

```bash
npm run dev
```

Open the local URL shown in the terminal.

What to check:

- You can sign in locally.
- The dashboard loads.
- KPI cards show values.
- The Risk Queue has finance rows.
- Selecting a transaction updates the detail panel.
- The Cost Centre Summary shows budget variance.

> CHECK BEFORE DELIVERY:
> The audit found that the local unauthenticated screen may still say "Blank App." Avoid showing this screen live or update the label before delivery.

### Step 9 - Deploy the app

Run:

```bash
npx rayfin up
```

After deployment, copy the App URL from the CLI output or Fabric portal.

In the Fabric portal:

1. Open the `Renishaw-FabricApps` workspace.
1. Open the `Renishaw Finance Control Tower` Fabric App item.
1. Confirm the App URL is available.
1. Open the app.
1. Confirm Fabric SSO works.

### Step 10 - Validate SQL database data

In the Fabric portal:

1. Open the Fabric App item.
1. Open the SQL database child service.
1. Use the SQL query editor if available.
1. Check that transaction and budget target rows exist.

Example validation queries, adjusting table names if needed:

```sql
SELECT TOP 10 *
FROM FinanceTransactions;

SELECT TOP 10 *
FROM BudgetTargets;
```

> CHECK BEFORE DELIVERY:
> Rayfin-generated physical table names may differ from entity class names. Use the object explorer to confirm exact names.

## Frontend/pages explanation

| UI area | What the finance user does | Source |
| --- | --- | --- |
| KPI cards | Gets a quick risk snapshot | React calculations over SQL rows |
| Risk Queue | Selects an item needing review | `FinanceTransaction` |
| Transaction detail | Reads context, owner, due date, and commentary | `FinanceTransaction` |
| Cost Centre Summary | Compares loaded spend with budget targets | `FinanceTransaction` + `BudgetTarget` |

The end user does not edit data in this app. The app is a direct SQL-backed dashboard pattern.

## Deployment checklist

Before customer delivery:

- The app is already deployed.
- The presenter is already signed in.
- SQL rows are seeded.
- The Risk Queue is visible.
- The known demo rows load.
- The Fabric workspace is open in another tab.
- You do not show secret-bearing environment files.

## Troubleshooting

| Problem | Likely cause | Fix |
| --- | --- | --- |
| SQL database is empty | Seed script has not been run | Run `npm run seed:finance` or the correct seed script. |
| App loads but shows no rows | Data service cannot read entities or SQL is empty | Check seed output, Rayfin API URL, and entity registration. |
| Local sign-in page says "Blank App" | Blank-template metadata remains | Start demo already authenticated or update the label before delivery. |
| `db apply` fails | Schema mismatch or config issue | Inspect Rayfin output; do not force destructive changes blindly. |
| Deployed app cannot sign in | Fabric auth or permissions issue | Confirm Fabric App permissions and tenant setting. |
| Budget variance looks odd | Synthetic data is not period-perfect | Describe it as a synthetic finance snapshot. |

## Final summary - what you have built

You have built a direct-SQL Fabric App that demonstrates:

- Rayfin entity modelling
- Fabric Apps managed SQL storage
- generated typed data access
- an authenticated React frontend
- a finance risk queue and budget snapshot

Use this app to explain the baseline Fabric Apps pattern:

> Say this:
> "This app shows the simplest operational pattern: SQL data in the Fabric App backend, Rayfin-generated APIs, and a focused finance UI for review and exception handling."

