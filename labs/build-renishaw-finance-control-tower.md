# Build lab - Renishaw Finance Control Tower

## Lab overview

In this lab you build the **Renishaw Finance Control Tower** app from scratch.

This is the first, simpler Fabric Apps demo. It shows how a Rayfin-built Fabric App can use the Fabric Apps managed SQL database as an operational store, then expose that data through a customer-facing finance review UI.

You will create:

1. A new Fabric App item in Microsoft Fabric.
1. A local Rayfin project from the blank template.
1. Two Rayfin data entities:
   - `FinanceTransaction`
   - `BudgetTarget`
1. Synthetic finance seed data.
1. A simple React finance dashboard.
1. A deployed Fabric App that reads finance data from the Fabric Apps SQL database.

> Presenter note:
> This app demonstrates the **direct SQL pattern**. It does not use a semantic model. The second app, `Finance App`, demonstrates the SQL-to-semantic-model pattern.

## Business scenario

Renishaw finance users need a lightweight internal app to review finance control items such as:

- supplier spend needing attention
- high-risk invoices
- accruals awaiting review
- capex approval threshold items
- working-capital concerns
- cost-centre budget pressure

The data in this lab is synthetic and illustrative. It is not real Renishaw financial data.

> Say this:
> "This app is intentionally focused. It shows a finance user which items need review, who owns them, why they matter, and how they relate to cost-centre budget targets."

## What this app demonstrates

| Capability | What you build |
| --- | --- |
| Fabric App item | A deployed app item inside a Fabric workspace. |
| Rayfin SDK/CLI | The local development and deployment framework. |
| Managed SQL database | Fabric Apps creates the app-owned SQL store. |
| Generated API layer | Rayfin exposes entity data through typed data access / generated GraphQL APIs. |
| React frontend | A finance dashboard with KPIs, risk queue, details, and budget summary. |

## Architecture summary

```text
Synthetic seed data
        |
        v
Rayfin seed script or seed utility
        |
        v
Fabric Apps managed SQL database
        |
        v
Rayfin generated data API
        |
        v
React Finance Control Tower UI
```

This app reads from SQL directly. It does not query a Power BI semantic model and it does not embed a report.

## Prerequisites

Before starting, confirm you have:

- A Fabric workspace assigned to Fabric capacity.
- Fabric Apps enabled in the tenant.
- Permission to create Fabric App items in the workspace.
- Node.js installed.
- Docker Desktop available if your Rayfin local workflow requires it.
- Visual Studio Code or another code editor.
- Access to the Rayfin CLI through `npm create @microsoft/rayfin@latest`.

Recommended names:

| Item | Name |
| --- | --- |
| Fabric workspace | `Renishaw-FabricApps` |
| Fabric App item | `Renishaw Finance Control Tower` |
| Local project folder | `renishaw-finance-control-tower` |

> CHECK BEFORE DELIVERY:
> Fabric Apps and Rayfin are preview-era capabilities. The exact generated folder structure can vary. If your scaffold uses `rayfin/data` instead of `data`, use the folder created by your scaffold and keep the same file names.

## Step 1 - Create the Fabric App item

1. Open [Fabric](https://app.fabric.microsoft.com).
1. Go to the workspace:

   ```text
   Renishaw-FabricApps
   ```

1. Select **New item**.
1. Search for **App**.
1. Select **App**.
1. Name the app:

   ```text
   Renishaw Finance Control Tower
   ```

1. Select **Create**.

> Presenter note:
> In the customer demo, do not spend time creating the app item live. Have it already created and deployed.

> Say this:
> "The app starts as a Fabric item. That means it sits inside the Fabric workspace and uses Fabric-managed services rather than separate hosting and identity plumbing."

## Step 2 - Create the local Rayfin project

Choose the parent folder where you want to create the app project.

Example:

```bash
cd "C:\Users\<your user>\Documents"
```

Run the scaffold command:

```bash
npm create @microsoft/rayfin@latest -- "Renishaw Finance Control Tower" --template blank --workspace "Renishaw-FabricApps"
```

When prompted, accept the defaults unless your environment requires a different workspace or app item.

The command creates a local project folder. If it creates a folder named after the app, rename it to a simpler folder name if you prefer:

```text
renishaw-finance-control-tower
```

Open the project:

```bash
cd "renishaw-finance-control-tower"
code .
```

Install dependencies:

```bash
npm install
```

Run the blank app:

```bash
npm run dev
```

Open the local URL shown in the terminal and confirm the blank app loads.

> CHECK BEFORE DELIVERY:
> If the generated folder has a different name, use that folder. The important point is that all remaining commands run from the local Rayfin project root.

## Step 3 - Understand the project folders

In VS Code, identify these areas:

| Area | Purpose |
| --- | --- |
| `rayfin.yml` | Rayfin/Fabric App deployment and service configuration. |
| `data` or `rayfin/data` | Rayfin entity models that become SQL-backed app data. |
| `src` | React frontend code. |
| `package.json` | NPM scripts for local dev, build, seed, and deployment. |

> Say this:
> "Rayfin gives us a developer workflow. We define data models, build a frontend, test locally, and deploy the app back into Fabric."

## Step 4 - Add the finance data entities

Find the folder where your scaffold expects data entities. In many Rayfin apps this is:

```text
data
```

If your scaffold created this instead, use it:

```text
rayfin/data
```

For the rest of this lab, the examples use `data`.

### Create `data/FinanceTransaction.ts`

Create a file:

```text
data/FinanceTransaction.ts
```

Paste:

```typescript
import {
  date,
  decimal,
  entity,
  role,
  text,
  uuid,
} from '@microsoft/rayfin-core';

@entity()
@role('authenticated', '*')
export class FinanceTransaction {
  @uuid() id!: string;
  @text({ min: 1, max: 30 }) transactionId!: string;
  @text({ min: 1, max: 30 }) transactionType!: string;
  @text({ min: 1, max: 80 }) costCentre!: string;
  @text({ min: 1, max: 120 }) supplier!: string;
  @text({ min: 1, max: 80 }) category!: string;
  @decimal() amountGbp!: number;
  @date() invoiceDate!: Date;
  @date() dueDate!: Date;
  @text({ min: 1, max: 20 }) forecastMonth!: string;
  @text({ min: 1, max: 30 }) status!: string;
  @text({ min: 1, max: 30 }) riskLevel!: string;
  @text({ min: 1, max: 160 }) varianceDriver!: string;
  @text({ min: 1, max: 80 }) financeOwner!: string;
  @text({ min: 1, max: 300 }) commentary!: string;
}
```

### Create `data/BudgetTarget.ts`

Create a file:

```text
data/BudgetTarget.ts
```

Paste:

```typescript
import {
  decimal,
  entity,
  role,
  text,
  uuid,
} from '@microsoft/rayfin-core';

@entity()
@role('authenticated', '*')
export class BudgetTarget {
  @uuid() id!: string;
  @text({ min: 1, max: 80 }) costCentre!: string;
  @text({ min: 1, max: 80 }) financeOwner!: string;
  @decimal() monthlyBudgetGbp!: number;
  @decimal() quarterlyBudgetGbp!: number;
  @decimal() riskToleranceGbp!: number;
  @text({ min: 1, max: 200 }) workingCapitalPriority!: string;
}
```

### Register the entities in `data/schema.ts`

Open or create:

```text
data/schema.ts
```

Add the entities to the schema type:

```typescript
import type { BudgetTarget } from './BudgetTarget.js';
import type { FinanceTransaction } from './FinanceTransaction.js';

export type AppSchema = {
  BudgetTarget: BudgetTarget;
  FinanceTransaction: FinanceTransaction;
};
```

If the blank template already created a schema type, do not create a second exported type. Add `BudgetTarget` and `FinanceTransaction` to the existing schema.

> CHECK BEFORE DELIVERY:
> If your scaffold uses a different schema type name, preserve that name and only add the two entity entries.

> Say this:
> "These TypeScript classes are the finance data contract. Rayfin uses them to create the SQL-backed entities and generated data API."

## Step 5 - Apply the SQL schema

From the project root, run:

```bash
npx rayfin up db apply
```

If this is the first time deploying the app and `db apply` cannot find the remote app, run:

```bash
npx rayfin up
```

Then retry:

```bash
npx rayfin up db apply
```

What to check:

- The command completes successfully.
- The Fabric App item exists in the workspace.
- The app has a SQL database child service.

> CHECK BEFORE DELIVERY:
> If Rayfin warns about destructive schema changes, stop and inspect the change before using `--force`.

## Step 6 - Add synthetic seed data

Create a folder:

```text
src/data
```

Create:

```text
src/data/financeSeedData.ts
```

Paste:

```typescript
export const financeTransactions = [
  {
    transactionId: 'FIN-0013',
    transactionType: 'Invoice',
    costCentre: 'Global Supply Chain',
    supplier: 'Kuehne Nagel',
    category: 'Freight',
    amountGbp: 58400,
    invoiceDate: '2026-07-02',
    dueDate: '2026-08-16',
    forecastMonth: '2026-08',
    status: 'Approved',
    riskLevel: 'High',
    varianceDriver: 'Expedited freight',
    financeOwner: 'Oliver Chen',
    commentary: 'Air freight used to protect committed customer delivery dates',
  },
  {
    transactionId: 'FIN-0021',
    transactionType: 'Invoice',
    costCentre: 'Facilities and Energy',
    supplier: 'National Grid',
    category: 'Energy',
    amountGbp: 73900,
    invoiceDate: '2026-07-03',
    dueDate: '2026-08-17',
    forecastMonth: '2026-08',
    status: 'Approved',
    riskLevel: 'High',
    varianceDriver: 'Energy price volatility',
    financeOwner: 'James Evans',
    commentary: 'Electricity cost above plan after production ramp-up',
  },
  {
    transactionId: 'FIN-0007',
    transactionType: 'Capex',
    costCentre: 'Additive Manufacturing',
    supplier: 'AM Solutions',
    category: 'Post-processing equipment',
    amountGbp: 86500,
    invoiceDate: '2026-07-11',
    dueDate: '2026-09-10',
    forecastMonth: '2026-09',
    status: 'Review',
    riskLevel: 'High',
    varianceDriver: 'Capex approval threshold',
    financeOwner: 'Noah Patel',
    commentary: 'Post-processing unit requested to reduce manual finishing queue',
  },
  {
    transactionId: 'FIN-0011',
    transactionType: 'Accrual',
    costCentre: 'Metrology Services',
    supplier: 'Field Service Partners',
    category: 'Contract labour',
    amountGbp: 22300,
    invoiceDate: '2026-07-14',
    dueDate: '2026-08-28',
    forecastMonth: '2026-08',
    status: 'Review',
    riskLevel: 'High',
    varianceDriver: 'Temporary contractor cover',
    financeOwner: 'Grace Hughes',
    commentary: 'Short-term contractor cover for metrology service backlog',
  },
];

export const budgetTargets = [
  {
    costCentre: 'Global Supply Chain',
    financeOwner: 'Oliver Chen',
    monthlyBudgetGbp: 310000,
    quarterlyBudgetGbp: 930000,
    riskToleranceGbp: 20000,
    workingCapitalPriority: 'Reduce expedited freight and premium logistics leakage',
  },
  {
    costCentre: 'Facilities and Energy',
    financeOwner: 'James Evans',
    monthlyBudgetGbp: 220000,
    quarterlyBudgetGbp: 660000,
    riskToleranceGbp: 18000,
    workingCapitalPriority: 'Separate energy volatility from controllable maintenance spend',
  },
  {
    costCentre: 'Additive Manufacturing',
    financeOwner: 'Noah Patel',
    monthlyBudgetGbp: 185000,
    quarterlyBudgetGbp: 555000,
    riskToleranceGbp: 12000,
    workingCapitalPriority: 'Control prototype spend while maintaining innovation throughput',
  },
  {
    costCentre: 'Metrology Services',
    financeOwner: 'Grace Hughes',
    monthlyBudgetGbp: 138000,
    quarterlyBudgetGbp: 414000,
    riskToleranceGbp: 8000,
    workingCapitalPriority: 'Keep service revenue enablement costs predictable',
  },
];
```

> Presenter note:
> These rows are intentionally small and memorable so you can explain them live.

## Step 7 - Create a seed script to insert rows into SQL

Create a folder:

```text
scripts
```

Create:

```text
scripts/seedFinanceData.ts
```

Use the Rayfin data client pattern generated by your project. The exact import path can vary by scaffold, so use the generated client already present in your project if one exists.

The script must do this:

1. Read `financeTransactions` and `budgetTargets`.
1. Check whether each transaction already exists by `transactionId`.
1. Insert it only if it does not exist.
1. Check whether each budget target already exists by `costCentre`.
1. Insert it only if it does not exist.
1. Print inserted/skipped counts.

Pseudo-implementation:

```typescript
import { financeTransactions, budgetTargets } from '../src/data/financeSeedData';
import { client } from '../src/rayfinClient';

async function seed() {
  let insertedTransactions = 0;
  let skippedTransactions = 0;
  let insertedBudgets = 0;
  let skippedBudgets = 0;

  for (const item of financeTransactions) {
    const existing = await client.data.FinanceTransaction
      .select(['id', 'transactionId'])
      .where({ transactionId: { eq: item.transactionId } })
      .first(1)
      .execute();

    if (existing.length > 0) {
      skippedTransactions += 1;
      continue;
    }

    await client.data.FinanceTransaction.create({
      ...item,
      invoiceDate: new Date(item.invoiceDate),
      dueDate: new Date(item.dueDate),
    });

    insertedTransactions += 1;
  }

  for (const item of budgetTargets) {
    const existing = await client.data.BudgetTarget
      .select(['id', 'costCentre'])
      .where({ costCentre: { eq: item.costCentre } })
      .first(1)
      .execute();

    if (existing.length > 0) {
      skippedBudgets += 1;
      continue;
    }

    await client.data.BudgetTarget.create(item);
    insertedBudgets += 1;
  }

  console.log(`Transactions inserted: ${insertedTransactions}`);
  console.log(`Transactions skipped: ${skippedTransactions}`);
  console.log(`Budget targets inserted: ${insertedBudgets}`);
  console.log(`Budget targets skipped: ${skippedBudgets}`);
}

seed().catch((error) => {
  console.error(error);
  process.exit(1);
});
```

> CHECK BEFORE DELIVERY:
> Replace `../src/rayfinClient` with the actual client helper generated by your project. If the project has no helper, create one using the Rayfin client configuration from the scaffold. Do not hardcode secrets.

Add a package script in `package.json`:

```json
{
  "scripts": {
    "seed:finance": "tsx scripts/seedFinanceData.ts"
  }
}
```

If `tsx` is not installed:

```bash
npm install --save-dev tsx
```

Run the seed:

```bash
npm run seed:finance
```

Run it twice. The second run should skip existing records rather than duplicate them.

> Presenter note:
> The customer does not need to see the seed script. It is preparation work so the app has SQL rows to read.

## Step 8 - Create the finance data service

Create:

```text
src/services/financeDataService.ts
```

This service should read rows from the Rayfin data API.

Use your generated Rayfin client helper and implement two functions:

```typescript
import { client } from '../rayfinClient';

export async function getFinanceTransactions() {
  return client.data.FinanceTransaction
    .select([
      'id',
      'transactionId',
      'transactionType',
      'costCentre',
      'supplier',
      'category',
      'amountGbp',
      'invoiceDate',
      'dueDate',
      'forecastMonth',
      'status',
      'riskLevel',
      'varianceDriver',
      'financeOwner',
      'commentary',
    ])
    .orderBy({ dueDate: 'asc' })
    .first(1000)
    .execute();
}

export async function getBudgetTargets() {
  return client.data.BudgetTarget
    .select([
      'id',
      'costCentre',
      'financeOwner',
      'monthlyBudgetGbp',
      'quarterlyBudgetGbp',
      'riskToleranceGbp',
      'workingCapitalPriority',
    ])
    .orderBy({ costCentre: 'asc' })
    .first(1000)
    .execute();
}
```

> CHECK BEFORE DELIVERY:
> Adjust field selection syntax to match the Rayfin client generated by your project. The important behavior is direct reads from `FinanceTransaction` and `BudgetTarget`.

## Step 9 - Build the React app UI

Open the main app file. In many blank projects this is:

```text
src/App.tsx
```

If your scaffold uses pages, find the main page and replace the blank content with a finance dashboard.

The UI must include:

1. Header:
   - `Renishaw Finance Control Tower`
   - Short description of finance risk review.
1. KPI cards:
   - Total spend reviewed.
   - High-risk items.
   - Items in review.
1. Risk Queue table:
   - Transaction ID.
   - Cost centre.
   - Supplier.
   - Amount.
   - Risk.
   - Status.
1. Transaction detail panel:
   - Supplier.
   - Category.
   - Amount.
   - Due date.
   - Variance driver.
   - Commentary.
   - Finance owner.
1. Cost Centre Summary:
   - Cost centre.
   - Finance owner.
   - Actual spend.
   - Monthly budget.
   - Variance.
   - Working-capital priority.

Business logic:

```typescript
const totalSpend = transactions.reduce(
  (sum, item) => sum + Number(item.amountGbp),
  0
);

const highRiskItems = transactions.filter(
  (item) => item.riskLevel === 'High'
);

const reviewItems = transactions.filter(
  (item) => item.status === 'Review'
);
```

Cost-centre actual spend:

```typescript
const spendForCostCentre = transactions
  .filter((item) => item.costCentre === budget.costCentre)
  .reduce((sum, item) => sum + Number(item.amountGbp), 0);

const variance = spendForCostCentre - Number(budget.monthlyBudgetGbp);
```

> Say this:
> "The app is intentionally more focused than a report. It shows the finance user the items that need review and the supporting context."

## Step 10 - Add loading, empty, and error states

The dashboard should show:

| State | Message |
| --- | --- |
| Loading | `Loading finance data...` |
| Empty | `No finance transactions found. Run npm run seed:finance, then refresh.` |
| Error | A clear error explaining that finance data could not be loaded. |

This is important for demo reliability.

## Step 11 - Run locally

Run:

```bash
npm run dev
```

Open the local URL.

Validate:

- The app loads.
- KPI cards show values.
- The Risk Queue shows seeded rows.
- Clicking a row updates the detail panel.
- Cost-centre summary shows budget variance.
- Refreshing the browser still shows data because rows are now in SQL.

## Step 12 - Deploy to Fabric

Run:

```bash
npx rayfin up
```

When deployment completes:

1. Open Fabric.
1. Go to `Renishaw-FabricApps`.
1. Open `Renishaw Finance Control Tower`.
1. Open the App URL.
1. Confirm Fabric SSO works.
1. Confirm the dashboard loads.

## Step 13 - Validate the SQL database

In Fabric:

1. Open the `Renishaw Finance Control Tower` app item.
1. Open the SQL database child service.
1. Use the object explorer to find the generated tables.
1. Run row-count queries.

Example, adjusting physical table names if required:

```sql
SELECT COUNT(*) AS transaction_count
FROM FinanceTransactions;

SELECT COUNT(*) AS budget_target_count
FROM BudgetTargets;
```

> CHECK BEFORE DELIVERY:
> Rayfin-generated physical table names may differ from class names. Use the SQL object explorer to confirm the exact names.

## What the finance user is meant to do

The finance user:

1. Opens the Finance Control Tower.
1. Looks at the headline KPI cards.
1. Reviews the Risk Queue.
1. Clicks a high-risk item.
1. Reads the reason for the variance.
1. Checks owner and due date.
1. Uses the cost-centre summary to understand budget pressure.

This app does not capture review notes or actions. That capability is shown in the second app, `Finance App`.

## How the app reads and writes data

| Operation | Where it happens |
| --- | --- |
| Data model definition | Rayfin entity files |
| SQL schema creation | `npx rayfin up db apply` / `npx rayfin up` |
| Demo data insertion | `npm run seed:finance` |
| Runtime reads | React app through Rayfin typed data client |
| End-user writes | Not implemented in this app |

> Presenter note:
> Do not claim this app has workflow write-back. It is the direct SQL read pattern.

## Troubleshooting

| Problem | Likely cause | Fix |
| --- | --- | --- |
| App shows no rows | Seed script has not been run | Run `npm run seed:finance`. |
| SQL database is empty | Schema exists but no data inserted | Run the seed script and validate row counts. |
| Seed script duplicates rows | Idempotency check missing | Check by `transactionId` and `costCentre` before insert. |
| Seed script cannot connect | Missing Rayfin client config or auth | Use the generated Rayfin client/environment pattern; do not hardcode secrets. |
| `db apply` fails | Entity registration or schema mismatch | Check `data/schema.ts` and Rayfin output. |
| App works locally but not deployed | Fabric auth/deployment config issue | Check `rayfin.yml`, app permissions, and App URL. |
| Sign-in page has blank-template wording | Starter template text remains | Update the unauthenticated screen before delivery or start already signed in. |

## Demo preparation checklist

Before the customer session:

- The app is deployed.
- You are signed in.
- SQL rows exist.
- KPI cards load.
- Risk Queue rows load.
- You know which row you will click first.
- You can show the SQL child service if needed.
- You do not show `.env` files.
- You do not describe the synthetic data as real Renishaw data.

## Final summary - what you have built

You have built a direct-SQL Fabric App with Rayfin:

- Fabric App item in the `Renishaw-FabricApps` workspace.
- Rayfin-defined finance entities.
- Fabric Apps managed SQL database.
- Synthetic finance rows inserted into SQL.
- React dashboard reading from the generated Rayfin data API.
- Finance control UI for risk, owner, due date, variance, and budget context.

> Say this:
> "This first app shows the baseline Fabric Apps pattern: app-owned SQL data, Rayfin-generated APIs, and a focused finance UI. It is different from a Power BI report because it is an application experience, but it does not yet use a semantic model or workflow write-back."

