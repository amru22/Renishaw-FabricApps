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

First choose the **parent folder** where you want the project to be created. The Rayfin CLI creates the generated project folder inside whichever folder you run the command from.

For example, if you want the project under Documents:

```bash
cd "C:\Users\<your user>\Documents"
```

Then run the command from the Fabric App item. Because this lab is not a to-do list app, use the blank template:

```bash
npm create @microsoft/rayfin@latest -- "Renishaw Finance Control Tower" --template blank --workspace "<your workspace name>"
```

The command creates a generated folder in your current location. It will usually be named after the app:

```text
C:\Users\<your user>\Documents\Renishaw Finance Control Tower
```

Now open that generated folder:

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

If asked why you used `--template blank`, say:

> "I am using the blank template because this is a custom finance app. I only want the Fabric Apps project structure, not the sample to-do app experience."

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

### Register the entities in `rayfin/data/schema.ts`

Open:

```text
rayfin/data/schema.ts
```

Add the two model types to the schema. The exact type name may be different in your generated project, but the finished file should follow this pattern:

```typescript
import type { BudgetTarget } from './BudgetTarget.js';
import type { FinanceTransaction } from './FinanceTransaction.js';

export type AppSchema = {
  BudgetTarget: BudgetTarget;
  FinanceTransaction: FinanceTransaction;
};
```

If your blank template already has an exported schema type, do not create a second one. Add `BudgetTarget` and `FinanceTransaction` to the existing exported type instead.

### What to explain

Say:

> "Registering the entities tells Fabric Apps which TypeScript models belong in this application schema. Once registered, they can be used by the generated data API."

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

## Step 6 - Add simple local demo data

At this point, do **not** build a CSV loading process yet. That adds unnecessary complexity for a 20-25 minute customer demo.

Instead, create a small TypeScript data file that gives the app something to display immediately.

From inside your Fabric App project folder, create this folder:

```bash
mkdir src\data
```

Create this file:

```text
src\data\financeData.ts
```

Paste this into `src\data\financeData.ts`:

```typescript
export const financeTransactions = [
  {
    transactionId: 'FIN-0013',
    transactionType: 'Invoice',
    costCentre: 'Global Supply Chain',
    supplier: 'Kuehne Nagel',
    category: 'Freight',
    amountGbp: 58400,
    dueDate: '2026-08-16',
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
    dueDate: '2026-08-17',
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
    dueDate: '2026-09-10',
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
    dueDate: '2026-08-28',
    status: 'Review',
    riskLevel: 'High',
    varianceDriver: 'Temporary contractor cover',
    financeOwner: 'Grace Hughes',
    commentary: 'Short-term contractor cover for metrology service backlog',
  },
];
```

Your project should now contain:

```text
src
  data
    financeData.ts
```

The full CSV files in this repository are still useful as supporting material:

- `data/finance_app_transactions.csv`
- `data/finance_app_budget_targets.csv`

However, for the first working demo, use the local TypeScript data above. After the demo is working, you can decide whether to replace this with a proper seed script that writes records through the generated GraphQL client.

### What to explain

Say:

> "For the demo, I have preloaded a small synthetic finance dataset so we can focus on the Fabric Apps experience rather than spending the customer session entering data manually."

## Step 7 - Build the app screen

"Build the app screen" means editing the React page so the browser shows the finance demo UI.

Open the main app file. In most blank templates this is:

```text
src\App.tsx
```

If you do not have `src\App.tsx`, look for one of these instead:

```text
src\App.jsx
src\main.tsx
src\pages\index.tsx
```

Replace the contents of `src\App.tsx` with this:

```tsx
import { useState } from 'react';
import type { CSSProperties } from 'react';
import { financeTransactions } from './data/financeData';

type FinanceTransaction = (typeof financeTransactions)[number];

export default function App() {
  const [selectedTransaction, setSelectedTransaction] =
    useState<FinanceTransaction>(financeTransactions[0]);

  const totalSpend = financeTransactions.reduce(
    (sum, item) => sum + item.amountGbp,
    0
  );

  const highRiskItems = financeTransactions.filter(
    (item) => item.riskLevel === 'High'
  );

  const reviewItems = financeTransactions.filter(
    (item) => item.status === 'Review'
  );

  return (
    <main style={styles.page}>
      <section style={styles.hero}>
        <p style={styles.eyebrow}>Renishaw demo scenario</p>
        <h1 style={styles.title}>Finance Control Tower</h1>
        <p style={styles.subtitle}>
          A Fabric App for reviewing supplier spend, invoice risk, forecast
          variance, and working-capital impact across finance cost centres.
        </p>
      </section>

      <section style={styles.cardGrid}>
        <div style={styles.card}>
          <p style={styles.cardLabel}>Total spend reviewed</p>
          <p style={styles.cardValue}>{formatCurrency(totalSpend)}</p>
        </div>

        <div style={styles.card}>
          <p style={styles.cardLabel}>High-risk items</p>
          <p style={styles.cardValue}>{highRiskItems.length}</p>
        </div>

        <div style={styles.card}>
          <p style={styles.cardLabel}>Items in review</p>
          <p style={styles.cardValue}>{reviewItems.length}</p>
        </div>
      </section>

      <section style={styles.contentGrid}>
        <div style={styles.panel}>
          <h2 style={styles.sectionTitle}>Risk Queue</h2>
          <p style={styles.sectionText}>
            Finance items that need attention because they are high risk or
            currently in review.
          </p>

          <table style={styles.table}>
            <thead>
              <tr>
                <th style={styles.th}>ID</th>
                <th style={styles.th}>Cost centre</th>
                <th style={styles.th}>Supplier</th>
                <th style={styles.th}>Amount</th>
                <th style={styles.th}>Risk</th>
                <th style={styles.th}>Status</th>
              </tr>
            </thead>

            <tbody>
              {financeTransactions.map((item) => (
                <tr
                  key={item.transactionId}
                  onClick={() => setSelectedTransaction(item)}
                  style={{
                    ...styles.row,
                    background:
                      selectedTransaction.transactionId === item.transactionId
                        ? '#eef6ff'
                        : 'white',
                  }}
                >
                  <td style={styles.td}>{item.transactionId}</td>
                  <td style={styles.td}>{item.costCentre}</td>
                  <td style={styles.td}>{item.supplier}</td>
                  <td style={styles.td}>{formatCurrency(item.amountGbp)}</td>
                  <td style={styles.td}>
                    <span style={styles.riskBadge}>{item.riskLevel}</span>
                  </td>
                  <td style={styles.td}>{item.status}</td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>

        <aside style={styles.detailPanel}>
          <p style={styles.eyebrow}>Selected transaction</p>
          <h2 style={styles.sectionTitle}>
            {selectedTransaction.transactionId}
          </h2>

          <div style={styles.detailList}>
            <Detail label="Supplier" value={selectedTransaction.supplier} />
            <Detail label="Category" value={selectedTransaction.category} />
            <Detail
              label="Amount"
              value={formatCurrency(selectedTransaction.amountGbp)}
            />
            <Detail label="Due date" value={selectedTransaction.dueDate} />
            <Detail label="Risk level" value={selectedTransaction.riskLevel} />
            <Detail label="Status" value={selectedTransaction.status} />
            <Detail
              label="Finance owner"
              value={selectedTransaction.financeOwner}
            />
            <Detail
              label="Variance driver"
              value={selectedTransaction.varianceDriver}
            />
          </div>

          <div style={styles.commentaryBox}>
            <p style={styles.cardLabel}>Finance commentary</p>
            <p style={styles.commentary}>{selectedTransaction.commentary}</p>
          </div>
        </aside>
      </section>
    </main>
  );
}

function Detail({ label, value }: { label: string; value: string }) {
  return (
    <div>
      <p style={styles.detailLabel}>{label}</p>
      <p style={styles.detailValue}>{value}</p>
    </div>
  );
}

function formatCurrency(value: number) {
  return new Intl.NumberFormat('en-GB', {
    style: 'currency',
    currency: 'GBP',
    maximumFractionDigits: 0,
  }).format(value);
}

const styles: Record<string, CSSProperties> = {
  page: {
    minHeight: '100vh',
    background: '#f5f7fb',
    color: '#172033',
    fontFamily:
      'Segoe UI, system-ui, -apple-system, BlinkMacSystemFont, sans-serif',
    padding: '40px',
  },
  hero: {
    marginBottom: '28px',
  },
  eyebrow: {
    margin: 0,
    color: '#0078d4',
    fontSize: '13px',
    fontWeight: 700,
    textTransform: 'uppercase',
    letterSpacing: '0.08em',
  },
  title: {
    margin: '8px 0',
    fontSize: '42px',
    lineHeight: 1.1,
  },
  subtitle: {
    margin: 0,
    maxWidth: '820px',
    color: '#536174',
    fontSize: '18px',
    lineHeight: 1.5,
  },
  cardGrid: {
    display: 'grid',
    gridTemplateColumns: 'repeat(3, minmax(0, 1fr))',
    gap: '18px',
    marginBottom: '24px',
  },
  card: {
    background: 'white',
    borderRadius: '18px',
    padding: '22px',
    boxShadow: '0 10px 30px rgba(15, 23, 42, 0.08)',
  },
  cardLabel: {
    margin: 0,
    color: '#667085',
    fontSize: '14px',
  },
  cardValue: {
    margin: '8px 0 0',
    fontSize: '32px',
    fontWeight: 700,
  },
  contentGrid: {
    display: 'grid',
    gridTemplateColumns: '2fr 1fr',
    gap: '24px',
    alignItems: 'start',
  },
  panel: {
    background: 'white',
    borderRadius: '18px',
    padding: '22px',
    boxShadow: '0 10px 30px rgba(15, 23, 42, 0.08)',
  },
  detailPanel: {
    background: 'white',
    borderRadius: '18px',
    padding: '22px',
    boxShadow: '0 10px 30px rgba(15, 23, 42, 0.08)',
    position: 'sticky',
    top: '24px',
  },
  sectionTitle: {
    margin: '4px 0 6px',
    fontSize: '24px',
  },
  sectionText: {
    margin: '0 0 18px',
    color: '#667085',
  },
  table: {
    width: '100%',
    borderCollapse: 'collapse',
  },
  th: {
    textAlign: 'left',
    padding: '12px',
    borderBottom: '1px solid #e5e7eb',
    color: '#667085',
    fontSize: '13px',
  },
  td: {
    padding: '14px 12px',
    borderBottom: '1px solid #eef2f7',
    fontSize: '14px',
  },
  row: {
    cursor: 'pointer',
  },
  riskBadge: {
    display: 'inline-block',
    background: '#fee4e2',
    color: '#b42318',
    padding: '4px 10px',
    borderRadius: '999px',
    fontSize: '12px',
    fontWeight: 700,
  },
  detailList: {
    display: 'grid',
    gap: '14px',
    marginTop: '20px',
  },
  detailLabel: {
    margin: 0,
    color: '#667085',
    fontSize: '13px',
  },
  detailValue: {
    margin: '3px 0 0',
    fontWeight: 650,
  },
  commentaryBox: {
    marginTop: '22px',
    background: '#f8fafc',
    borderRadius: '14px',
    padding: '16px',
  },
  commentary: {
    margin: '8px 0 0',
    lineHeight: 1.5,
  },
};
```

After saving the file, your project should contain:

```text
src
  data
    financeData.ts
  App.tsx
```

What you should see in the browser:

1. A **Finance Control Tower** title area.
1. Three overview cards.
1. A clickable **Risk Queue** table.
1. A **Selected transaction** panel that changes when you click a row.

### What to explain

Say:

> "This first screen is intentionally simple. It gives us a customer-ready demo flow: headline finance exposure, a risk queue, and transaction-level context."

## Later improvement - connect the UI to the Fabric Apps backend

The screen above uses local TypeScript data so you can get unstuck and create the demo quickly. After that works, you can improve it by replacing `financeTransactions` with data read through the generated Rayfin GraphQL client.

The CSV-to-model mapping is:

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

For the Renishaw customer demo, the local TypeScript data is enough if the purpose is to explain the Fabric Apps pattern. Use the backend connection only if you have time to prepare it properly before the session.

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
1. Open `FIN-0007` for the capex approval threshold.

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
