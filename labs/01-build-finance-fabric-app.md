# Lab 01 - Build the Renishaw Finance Control Fabric App

## Lab goal

Build a short, finance-focused Microsoft Fabric App that demonstrates how a business user can move from a simple finance control process to a governed internal application running on Fabric.

The finished app is a **Finance Control Tower** for reviewing spend, forecast variance, invoice risk, and working-capital impact across Renishaw-style cost centres.

## Target timing

This build is intended as preparation before the customer session. If you run it slowly and explain each step, it can also be presented as a 20-25 minute technical walkthrough.

| Section | Time |
| --- | ---: |
| Position the scenario and prerequisites | 2 min |
| Create the Fabric App item and local project | 4 min |
| Explain the generated project structure | 3 min |
| Add finance data models | 5 min |
| Add finance data and app screens | 7 min |
| Deploy and validate in Fabric | 4 min |

## What you will build

The app has four simple experiences:

1. **Finance overview** - headline spend, forecast, and risk indicators.
1. **Cost centre view** - spend by cost centre against budget targets.
1. **Risk queue** - invoices, accruals, and capex items requiring finance review.
1. **Transaction detail** - business explanation, variance driver, owner, status, and due date.

## Why this scenario works for Renishaw

Use this positioning:

> "This is not a generic app demo. This is the sort of controlled finance process a manufacturing organisation often has: cost centres, supplier spend, energy volatility, expedited freight, capex approval, and working-capital impact. The point is to show that Fabric Apps lets us build a governed operational app close to the data platform, rather than exporting data into disconnected spreadsheets or asking IT to build a full custom application from scratch."

## Prerequisites

Before starting, make sure you have:

- A Fabric workspace assigned to Fabric capacity.
- Fabric Apps enabled by the tenant admin under **Tenant settings > Fabric Apps (preview)**.
- Workspace **Contributor**, **Member**, or **Admin** permissions.
- Node.js 20 or later.
- Docker Desktop running for local development.
- GitHub Copilot or an editor such as Visual Studio Code.
- The generated lab data from this repository:
  - `data/finance_app_transactions.csv`
  - `data/finance_app_budget_targets.csv`

## Step 1 - Create the Fabric App item

1. Open [Fabric](https://app.fabric.microsoft.com).
1. Go to the workspace you want to use for the demo.
1. Select **New item**.
1. Search for **App**.
1. Select **App**.
1. Name it:

   ```text
   Renishaw Finance Control Tower
   ```

1. Select **Create**.

### What to explain

Say:

> "I am starting from the Fabric workspace because Fabric Apps are first-class Fabric items. That means the app sits alongside the data, notebooks, warehouses, lakehouses, reports, and governance model. It is not a detached web app that we have to host and secure separately."

Explain that Fabric Apps provides:

- A managed app backend.
- A SQL database in Fabric generated from TypeScript data models.
- GraphQL APIs generated from the model.
- Fabric SSO for deployed apps.
- Static hosting for the frontend.

## Step 2 - Download or scaffold the local project

In the Fabric App item, use the CLI command shown in the portal. It will look similar to this:

```bash
npm create @microsoft/rayfin@latest -- "Renishaw Finance Control Tower" --template todoapp --workspace "<your workspace name>"
```

Then open the generated folder:

```bash
cd "Renishaw Finance Control Tower"
code .
```

Install dependencies if the scaffold did not already do this:

```bash
npm install
```

Start local development:

```bash
npm run dev
```

### What to explain

Say:

> "The Rayfin CLI gives me the local development workflow for Fabric Apps. I can develop locally, test against the Fabric-backed services, and then publish the app with one deployment command."

The important message for a customer is that the app is still software-engineering friendly:

- It can live in GitHub.
- It can be edited in VS Code.
- It can be reviewed through pull requests.
- It can be deployed repeatedly.

## Step 3 - Explain the generated project structure

Open the generated project and point out these areas:

| Area | What it means |
| --- | --- |
| `rayfin/` | Fabric Apps configuration and backend model area. |
| `rayfin/data/` | TypeScript data models that become database tables and GraphQL APIs. |
| `src/` | Frontend app code. |
| `rayfin.yml` | Deployment and service configuration. |
| `.env` files | Local settings created by the CLI. Do not commit secrets. |

### What to explain

Say:

> "The important concept is model-first development. I define the business entities once, and Fabric Apps uses those definitions to create the database schema and the API surface."

## Step 4 - Add the finance data models

Create two model files under `rayfin/data/`.

### `rayfin/data/FinanceTransaction.ts`

Use the latest decorator imports generated by your scaffolded project. The model should represent the fields below.

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

### `rayfin/data/BudgetTarget.ts`

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

> Note: If your generated template uses slightly different decorator import names, keep the same business fields but align with the generated template style. The concept to explain is more important than the exact import formatting.

### What to explain

Say:

> "These two classes are the finance domain model. I am not manually building tables, controllers, and APIs. I am defining the finance entities, and Fabric Apps generates the persistence and GraphQL access pattern."

Explain the two entities:

- **FinanceTransaction** is the operational finance queue: invoices, accruals, forecasts, and capex requests.
- **BudgetTarget** is the reference table for cost centre budget and risk tolerance.

## Step 5 - Apply the schema locally

Run:

```bash
npx rayfin up db apply
```

If you are still developing locally, restart the dev server after schema changes:

```bash
npm run dev
```

### What to explain

Say:

> "This is where the model becomes real infrastructure. The schema is generated from code, which means the application model can be source controlled, peer reviewed, and redeployed safely."

## Step 6 - Load the synthetic finance data

Use the two CSV files in this repository as your source data:

- `data/finance_app_transactions.csv`
- `data/finance_app_budget_targets.csv`

For a short demo, use one of these practical loading options:

| Option | Best for | Approach |
| --- | --- | --- |
| Manual seed screen | Customer-facing demo | Add a temporary admin page that reads the CSV rows and creates records through the generated GraphQL client. |
| Scripted seed | Repeatable preparation | Create a local seed script that parses the CSV files and calls `client.data.FinanceTransaction.create()` and `client.data.BudgetTarget.create()`. |
| UI entry of sample records | Very small demo | Manually enter 5-8 rows through the app interface. |

For this lab, use the **scripted seed** approach during preparation so you do not spend demo time typing data.

### Seed mapping

Map CSV columns to model fields as follows:

| CSV column | Model field |
| --- | --- |
| `transaction_id` | `transactionId` |
| `transaction_type` | `transactionType` |
| `cost_centre` | `costCentre` |
| `supplier` | `supplier` |
| `category` | `category` |
| `amount_gbp` | `amountGbp` |
| `invoice_date` | `invoiceDate` |
| `due_date` | `dueDate` |
| `forecast_month` | `forecastMonth` |
| `status` | `status` |
| `risk_level` | `riskLevel` |
| `variance_driver` | `varianceDriver` |
| `finance_owner` | `financeOwner` |
| `commentary` | `commentary` |

### What to explain

Say:

> "The data is synthetic, but it is deliberately shaped like a real finance control process: supplier invoices, accruals, forecast items, capex, risk levels, due dates, and owners."

## Step 7 - Build the app screens

Create a simple frontend with four sections.

### 1. Finance overview

Show:

- Total gross spend.
- Forecast recoveries or savings.
- Net forecast impact.
- Number of high-risk items.
- Items currently in review.

Use this logic:

```typescript
const totalSpend = transactions
  .filter((item) => item.amountGbp > 0)
  .reduce((sum, item) => sum + item.amountGbp, 0);

const totalRecoveries = transactions
  .filter((item) => item.amountGbp < 0)
  .reduce((sum, item) => sum + item.amountGbp, 0);

const highRiskItems = transactions.filter((item) => item.riskLevel === 'High');
const reviewItems = transactions.filter((item) => item.status === 'Review');
```

### 2. Cost centre view

Show:

- Cost centre.
- Finance owner.
- Actual spend.
- Monthly budget.
- Variance to budget.
- Working-capital priority.

### 3. Risk queue

Filter to:

- `riskLevel = High`, or
- `status = Review`, or
- `amountGbp` greater than the cost centre risk tolerance.

This is the best screen to demo because it gives a clear business story.

### 4. Transaction detail

When a user selects a transaction, show:

- Supplier.
- Category.
- Amount.
- Due date.
- Variance driver.
- Commentary.
- Finance owner.
- Status.

### What to explain

Say:

> "This is the app layer that sits on top of the Fabric-managed backend. The business user sees a finance control experience, while the developer still gets a typed API and source-controlled model."

## Step 8 - Run locally and check the story

Run:

```bash
npm run dev
```

Check these demo scenarios:

1. Open the finance overview and show the headline cards.
1. Filter the risk queue to **High**.
1. Open `FIN-0013` for expedited freight.
1. Open `FIN-0021` for energy volatility.
1. Compare Corporate IT spend against the budget target.

### What to explain

Say:

> "Before I publish this, I can validate the finance story locally. That gives me the speed of local development while still building towards a governed Fabric deployment."

## Step 9 - Deploy to Fabric

When the local app is ready, deploy:

```bash
npx rayfin up
```

The deployment creates or updates:

- The Fabric App item.
- The generated SQL database schema.
- Runtime settings.
- Static frontend hosting.
- The app URL.

### What to explain

Say:

> "This single deployment command is the handoff from local development into Fabric. Fabric hosts the app, the API, authentication, and the data service. That is why this is useful for internal operational applications."

## Step 10 - Validate in the Fabric portal

In the Fabric portal:

1. Open the Fabric App item.
1. Copy the **App URL**.
1. Open the app in a browser.
1. Confirm Fabric SSO sign-in works.
1. Open the Fabric App child services.
1. Show the SQL database child item.
1. Run a simple read query if available in your tenant:

   ```sql
   SELECT TOP 10 *
   FROM FinanceTransaction;
   ```

### What to explain

Say:

> "The app is now a governed Fabric item. I can manage it from the workspace, control access through Fabric permissions, and show the supporting data service as a child item."

## Suggested final state before the customer demo

Before the customer-facing demo, make sure:

- The deployed app opens from the Fabric App URL.
- The finance data is already loaded.
- The overview has visible totals.
- The risk queue has at least 4-6 interesting records.
- The transaction detail screen opens quickly.
- You have the Fabric workspace open in another tab.
- You have this demo script open: `labs/02-demo-script-finance-fabric-app.md`.

## Clean talk track for the build section

Use these phrases throughout:

- "Fabric Apps is a way to build governed data applications directly on Fabric."
- "The data model is defined in TypeScript, but Fabric generates the database and GraphQL API."
- "This avoids building boilerplate infrastructure for authentication, hosting, and persistence."
- "The app is still source-controlled and developer-friendly."
- "For a finance team, this is useful when the process is too structured for a spreadsheet but too lightweight for a large custom app project."
