# Build lab - Finance App

## Lab overview

In this lab you build the **Finance App** from scratch.

This is the richer Fabric Apps demo. It shows how a Rayfin-built Fabric App can:

1. store finance source data in the Fabric Apps managed SQL database
1. feed that SQL data into a Power BI/Fabric semantic model
1. query governed finance measures from the semantic model
1. store finance review actions back into Rayfin-managed SQL

The purpose is to show how a Fabric App is different from a Power BI report. The app does not just visualise finance data; it creates a workflow around governed finance metrics.

> Presenter note:
> This app demonstrates the **SQL plus semantic model plus write-back** pattern. The first app, `Renishaw Finance Control Tower`, demonstrates the simpler direct-SQL pattern.

## Business scenario

Renishaw finance users need to review cost-centre spend, supplier exposure, invoice risk, overdue amounts, working-capital impact, and follow-up actions.

The semantic model provides trusted finance measures. The Rayfin app provides the operational review experience:

- prioritised review queue
- transaction review drawer
- owner assignment
- finance notes
- next action
- escalation flag
- persisted workflow status

The data is synthetic and illustrative. Do not imply that it is real Renishaw financial data.

> Say this:
> "A report is excellent for analysis. This app uses governed finance metrics, but adds the workflow layer finance users need when they must act on those metrics."

## What you will create

1. A new Fabric App item called `FinanceApp`.
1. A local Rayfin project from the blank template.
1. Five SQL-backed analytical source entities:
   - `DimCostCentre`
   - `DimSupplier`
   - `DimCategory`
   - `DimDate`
   - `FactFinanceTransaction`
1. One writable workflow entity:
   - `FinanceReviewAction`
1. CSV files in the app project's `data` folder.
1. An import process that loads those CSV files into the SQL database.
1. A semantic model called `Renishaw Finance Control Semantic Model`.
1. A React app that queries the semantic model and writes review actions through Rayfin.

## Architecture summary

```text
CSV files in data folder
        |
        v
CSV import script
        |
        v
Fabric Apps managed SQL database
        |
        v
Power BI/Fabric semantic model
        |
        v
DAX queries from React app
        |
        v
Finance App analytical UI

Review drawer
        |
        v
Rayfin typed data API
        |
        v
FinanceReviewAction SQL table
```

Important:

- SQL source data is loaded from CSV.
- The semantic model imports from SQL.
- Analytical measures live in the semantic model.
- Review workflow state lives in Rayfin SQL.
- The app does not write back to the semantic model.

## Prerequisites

Before starting, confirm you have:

- A Fabric workspace assigned to capacity.
- Fabric Apps enabled in the tenant.
- Permission to create Fabric App items.
- Permission to create or deploy a semantic model.
- Node.js installed.
- Visual Studio Code or another editor.
- Access to the Rayfin CLI through `npm create @microsoft/rayfin@latest`.

Recommended names:

| Item | Name |
| --- | --- |
| Fabric workspace | `Renishaw-FabricApps` |
| Fabric App item | `FinanceApp` |
| Local project folder | `finance-app` |
| Semantic model | `Renishaw Finance Control Semantic Model` |
| Semantic model alias | `finance` |

> CHECK BEFORE DELIVERY:
> The semantic-model query path depends on the deployed Fabric App running inside Fabric. Test the final app from the Fabric App URL, not only from localhost.

## Step 1 - Create the Fabric App item

1. Open [Fabric](https://app.fabric.microsoft.com).
1. Go to the workspace:

   ```text
   Renishaw-FabricApps
   ```

1. Select **New item**.
1. Search for **App**.
1. Select **App**.
1. Name it:

   ```text
   FinanceApp
   ```

1. Select **Create**.

> Say this:
> "This creates the Fabric App item. Rayfin is what we use to build and deploy the app, but the running app is a Fabric item."

## Step 2 - Create the local Rayfin project

Choose the parent folder where you want the local project.

Example:

```bash
cd "C:\Users\<your user>\Documents"
```

Run:

```bash
npm create @microsoft/rayfin@latest -- "FinanceApp" --template blank --workspace "Renishaw-FabricApps"
```

Open the generated project folder:

```bash
cd "FinanceApp"
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

Confirm the blank app loads locally.

> CHECK BEFORE DELIVERY:
> If the scaffold creates a differently named folder, use that folder. All remaining commands should run from the project root.

## Step 3 - Add CSV files to the app project's data folder

In the app project, create:

```text
data/import
```

Copy these CSV files from this lab repository into `data/import`:

```text
data/finance_app_semantic_model/dim_cost_centre.csv
data/finance_app_semantic_model/dim_supplier.csv
data/finance_app_semantic_model/dim_category.csv
data/finance_app_semantic_model/dim_date.csv
data/finance_app_semantic_model/fact_finance_transaction.csv
```

Your app project should now contain:

```text
FinanceApp
  data
    import
      dim_cost_centre.csv
      dim_supplier.csv
      dim_category.csv
      dim_date.csv
      fact_finance_transaction.csv
```

> Presenter note:
> This is the source data for the semantic-model demo. We are not assuming the user already has local CSVs; the lab repository provides them.

## Step 4 - Create the Rayfin analytical entities

Use the entity folder created by your scaffold. It is usually:

```text
data
```

If your scaffold uses:

```text
rayfin/data
```

use that folder instead.

### Create `DimCostCentre.ts`

```typescript
import { decimal, entity, role, text, uuid } from '@microsoft/rayfin-core';

@entity()
@role('authenticated', 'read')
export class DimCostCentre {
  @uuid() id!: string;
  @text({ unique: true }) costCentreKey!: string;
  @text() costCentre!: string;
  @text() financeOwner!: string;
  @text() businessArea!: string;
  @decimal() monthlyBudgetGbp!: number;
  @decimal() quarterlyBudgetGbp!: number;
  @decimal() riskToleranceGbp!: number;
  @text() workingCapitalPriority!: string;
}
```

### Create `DimSupplier.ts`

```typescript
import { boolean, entity, role, text, uuid } from '@microsoft/rayfin-core';

@entity()
@role('authenticated', 'read')
export class DimSupplier {
  @uuid() id!: string;
  @text({ unique: true }) supplierKey!: string;
  @text() supplier!: string;
  @text() supplierType!: string;
  @text() country!: string;
  @boolean() strategicSupplierFlag!: boolean;
}
```

### Create `DimCategory.ts`

```typescript
import { boolean, entity, role, text, uuid } from '@microsoft/rayfin-core';

@entity()
@role('authenticated', 'read')
export class DimCategory {
  @uuid() id!: string;
  @text({ unique: true }) categoryKey!: string;
  @text() category!: string;
  @text() categoryGroup!: string;
  @boolean() controllableSpendFlag!: boolean;
}
```

### Create `DimDate.ts`

```typescript
import { boolean, date, entity, int, role, text, uuid } from '@microsoft/rayfin-core';

@entity()
@role('authenticated', 'read')
export class DimDate {
  @uuid() id!: string;
  @date() date!: Date;
  @int() year!: number;
  @text() quarter!: string;
  @int() monthNumber!: number;
  @text() monthName!: string;
  @int() monthSort!: number;
  @int() weekNumber!: number;
  @boolean() isMonthEnd!: boolean;
}
```

### Create `FactFinanceTransaction.ts`

```typescript
import { boolean, date, decimal, entity, int, role, text, uuid } from '@microsoft/rayfin-core';

@entity()
@role('authenticated', 'read')
export class FactFinanceTransaction {
  @uuid() id!: string;
  @text({ unique: true }) transactionId!: string;
  @text() transactionType!: string;
  @text() costCentreKey!: string;
  @text() supplierKey!: string;
  @text() categoryKey!: string;
  @date() invoiceDate!: Date;
  @date() dueDate!: Date;
  @text() forecastMonth!: string;
  @decimal() amountGbp!: number;
  @text() status!: string;
  @text() riskLevel!: string;
  @text() varianceDriver!: string;
  @int() paymentTermsDays!: number;
  @boolean() isCapex!: boolean;
  @boolean() isWorkingCapitalImpact!: boolean;
  @text() commentary!: string;
}
```

### Create `FinanceReviewAction.ts`

```typescript
import { boolean, date, entity, role, set, text, uuid } from '@microsoft/rayfin-core';

@entity()
@role('authenticated', '*')
export class FinanceReviewAction {
  @uuid() id!: string;
  @text({ unique: true }) transactionId!: string;
  @text({ optional: true }) assignedOwner?: string;
  @set('Unresolved', 'In Review', 'Reviewed', 'Escalated') reviewStatus!:
    | 'Unresolved'
    | 'In Review'
    | 'Reviewed'
    | 'Escalated';
  @text({ optional: true }) financeNote?: string;
  @text({ optional: true }) nextAction?: string;
  @boolean({ default: false }) escalated!: boolean;
  @date() updatedAt!: Date;
}
```

> Presenter note:
> The first five entities are read-only source data. `FinanceReviewAction` is the write-back entity.

## Step 5 - Register the entities

Open or create:

```text
data/schema.ts
```

Add:

```typescript
import type { DimCategory } from './DimCategory.js';
import type { DimCostCentre } from './DimCostCentre.js';
import type { DimDate } from './DimDate.js';
import type { DimSupplier } from './DimSupplier.js';
import type { FactFinanceTransaction } from './FactFinanceTransaction.js';
import type { FinanceReviewAction } from './FinanceReviewAction.js';

export type AppSchema = {
  DimCategory: DimCategory;
  DimCostCentre: DimCostCentre;
  DimDate: DimDate;
  DimSupplier: DimSupplier;
  FactFinanceTransaction: FactFinanceTransaction;
  FinanceReviewAction: FinanceReviewAction;
};
```

If your scaffold already has a schema type, add these entities to the existing type instead of creating a second one.

## Step 6 - Apply the SQL schema

Run:

```bash
npx rayfin up db apply
```

If this is the first deployment and the remote app is not initialised, run:

```bash
npx rayfin up
```

Then run:

```bash
npx rayfin up db apply
```

Check in Fabric:

1. Open the `FinanceApp` Fabric App item.
1. Open the SQL database child service.
1. Confirm the source tables and `FinanceReviewActions` table exist.

> CHECK BEFORE DELIVERY:
> Rayfin-generated physical SQL table names may differ from entity class names. Use the SQL object explorer to confirm exact names.

## Step 7 - Import the CSV files into the SQL database

The recommended lab approach is to import CSVs through a script that calls the Rayfin generated data API. This keeps the import aligned with the app entity model and avoids manually editing SQL tables.

Install a CSV parser:

```bash
npm install --save-dev csv-parse tsx
```

Create:

```text
scripts/importFinanceCsv.ts
```

The import script should:

1. Read the five CSV files from `data/import`.
1. Insert dimension rows first.
1. Insert fact rows after dimensions.
1. Check unique keys before inserting so it can be safely re-run.
1. Print inserted and skipped counts.

Use this mapping:

| CSV file | Entity | Business key to check before insert |
| --- | --- | --- |
| `dim_cost_centre.csv` | `DimCostCentre` | `costCentreKey` |
| `dim_supplier.csv` | `DimSupplier` | `supplierKey` |
| `dim_category.csv` | `DimCategory` | `categoryKey` |
| `dim_date.csv` | `DimDate` | `date` |
| `fact_finance_transaction.csv` | `FactFinanceTransaction` | `transactionId` |

The CSV files use snake_case column names. The Rayfin entities use camelCase field names. Map them explicitly:

| CSV column | Entity field |
| --- | --- |
| `cost_centre_key` | `costCentreKey` |
| `cost_centre` | `costCentre` |
| `finance_owner` | `financeOwner` |
| `business_area` | `businessArea` |
| `monthly_budget_gbp` | `monthlyBudgetGbp` |
| `quarterly_budget_gbp` | `quarterlyBudgetGbp` |
| `risk_tolerance_gbp` | `riskToleranceGbp` |
| `working_capital_priority` | `workingCapitalPriority` |
| `supplier_key` | `supplierKey` |
| `supplier_type` | `supplierType` |
| `strategic_supplier_flag` | `strategicSupplierFlag` |
| `category_key` | `categoryKey` |
| `category_group` | `categoryGroup` |
| `controllable_spend_flag` | `controllableSpendFlag` |
| `month_number` | `monthNumber` |
| `month_name` | `monthName` |
| `month_sort` | `monthSort` |
| `week_number` | `weekNumber` |
| `is_month_end` | `isMonthEnd` |
| `transaction_id` | `transactionId` |
| `transaction_type` | `transactionType` |
| `invoice_date` | `invoiceDate` |
| `due_date` | `dueDate` |
| `forecast_month` | `forecastMonth` |
| `amount_gbp` | `amountGbp` |
| `risk_level` | `riskLevel` |
| `variance_driver` | `varianceDriver` |
| `payment_terms_days` | `paymentTermsDays` |
| `is_capex` | `isCapex` |
| `is_working_capital_impact` | `isWorkingCapitalImpact` |

Implementation pattern:

```typescript
import fs from 'node:fs';
import path from 'node:path';
import { parse } from 'csv-parse/sync';
import { client } from '../src/rayfinClient';

function readCsv(fileName: string) {
  const filePath = path.join(process.cwd(), 'data', 'import', fileName);
  const content = fs.readFileSync(filePath, 'utf8');
  return parse(content, { columns: true, skip_empty_lines: true });
}

function toBoolean(value: string) {
  return value.toLowerCase() === 'true';
}

async function insertIfMissing(
  entity: any,
  keyField: string,
  keyValue: string | number | Date,
  createPayload: Record<string, unknown>
) {
  const existing = await entity
    .select(['id', keyField])
    .where({ [keyField]: { eq: keyValue } })
    .first(1)
    .execute();

  if (existing.length > 0) {
    return 'skipped';
  }

  await entity.create(createPayload);
  return 'inserted';
}

async function main() {
  const costCentres = readCsv('dim_cost_centre.csv');
  const suppliers = readCsv('dim_supplier.csv');
  const categories = readCsv('dim_category.csv');
  const dates = readCsv('dim_date.csv');
  const transactions = readCsv('fact_finance_transaction.csv');

  for (const row of costCentres) {
    await insertIfMissing(client.data.DimCostCentre, 'costCentreKey', row.cost_centre_key, {
      costCentreKey: row.cost_centre_key,
      costCentre: row.cost_centre,
      financeOwner: row.finance_owner,
      businessArea: row.business_area,
      monthlyBudgetGbp: Number(row.monthly_budget_gbp),
      quarterlyBudgetGbp: Number(row.quarterly_budget_gbp),
      riskToleranceGbp: Number(row.risk_tolerance_gbp),
      workingCapitalPriority: row.working_capital_priority,
    });
  }

  for (const row of suppliers) {
    await insertIfMissing(client.data.DimSupplier, 'supplierKey', row.supplier_key, {
      supplierKey: row.supplier_key,
      supplier: row.supplier,
      supplierType: row.supplier_type,
      country: row.country,
      strategicSupplierFlag: toBoolean(row.strategic_supplier_flag),
    });
  }

  for (const row of categories) {
    await insertIfMissing(client.data.DimCategory, 'categoryKey', row.category_key, {
      categoryKey: row.category_key,
      category: row.category,
      categoryGroup: row.category_group,
      controllableSpendFlag: toBoolean(row.controllable_spend_flag),
    });
  }

  for (const row of dates) {
    await insertIfMissing(client.data.DimDate, 'date', new Date(row.date), {
      date: new Date(row.date),
      year: Number(row.year),
      quarter: row.quarter,
      monthNumber: Number(row.month_number),
      monthName: row.month_name,
      monthSort: Number(row.month_sort),
      weekNumber: Number(row.week_number),
      isMonthEnd: toBoolean(row.is_month_end),
    });
  }

  for (const row of transactions) {
    await insertIfMissing(
      client.data.FactFinanceTransaction,
      'transactionId',
      row.transaction_id,
      {
        transactionId: row.transaction_id,
        transactionType: row.transaction_type,
        costCentreKey: row.cost_centre_key,
        supplierKey: row.supplier_key,
        categoryKey: row.category_key,
        invoiceDate: new Date(row.invoice_date),
        dueDate: new Date(row.due_date),
        forecastMonth: row.forecast_month,
        amountGbp: Number(row.amount_gbp),
        status: row.status,
        riskLevel: row.risk_level,
        varianceDriver: row.variance_driver,
        paymentTermsDays: Number(row.payment_terms_days),
        isCapex: toBoolean(row.is_capex),
        isWorkingCapitalImpact: toBoolean(row.is_working_capital_impact),
        commentary: row.commentary,
      }
    );
  }
}

main().catch((error) => {
  console.error(error);
  process.exit(1);
});
```

> CHECK BEFORE DELIVERY:
> Replace `../src/rayfinClient` with the actual Rayfin client helper generated by your project. If the blank project does not have one, create it using the Rayfin client setup from the scaffold. Do not hardcode secrets.

Add this script to `package.json`:

```json
{
  "scripts": {
    "import:finance-csv": "tsx scripts/importFinanceCsv.ts"
  }
}
```

Run:

```bash
npm run import:finance-csv
```

Run it a second time to confirm it skips existing rows.

Validate in the SQL database:

```sql
SELECT COUNT(*) FROM DimCostCentres;
SELECT COUNT(*) FROM DimSuppliers;
SELECT COUNT(*) FROM DimCategories;
SELECT COUNT(*) FROM DimDates;
SELECT COUNT(*) FROM FactFinanceTransactions;
```

> CHECK BEFORE DELIVERY:
> Adjust table names to the actual physical names generated by Rayfin.

## Step 8 - Create the semantic model

Create a new semantic model in Fabric:

1. Open the `Renishaw-FabricApps` workspace.
1. Select **New item**.
1. Create a semantic model using the SQL database child service as the source.
1. Name it:

   ```text
   Renishaw Finance Control Semantic Model
   ```

1. Add the five analytical source tables:
   - `DimCostCentre`
   - `DimSupplier`
   - `DimCategory`
   - `DimDate`
   - `FactFinanceTransaction`

Do not add `FinanceReviewAction` to the semantic model for this demo. It is app workflow state, not governed finance source data.

> CHECK BEFORE DELIVERY:
> The exact Fabric UI path for creating a semantic model from the Fabric Apps SQL database may vary. If the UI cannot create it directly, use the supported TMDL or Fabric/Power BI deployment path available in your environment.

## Step 9 - Configure relationships

In the semantic model, configure these relationships:

| From | To |
| --- | --- |
| `FactFinanceTransaction[costCentreKey]` | `DimCostCentre[costCentreKey]` |
| `FactFinanceTransaction[supplierKey]` | `DimSupplier[supplierKey]` |
| `FactFinanceTransaction[categoryKey]` | `DimCategory[categoryKey]` |
| `FactFinanceTransaction[invoiceDate]` | `DimDate[date]` |

Use single-direction filtering from dimension to fact.

## Step 10 - Create semantic model measures

Create these measures:

```DAX
Total Spend GBP =
CALCULATE (
    SUM ( FactFinanceTransaction[amountGbp] ),
    FactFinanceTransaction[amountGbp] > 0
)

Forecast Recoveries GBP =
CALCULATE (
    SUM ( FactFinanceTransaction[amountGbp] ),
    FactFinanceTransaction[amountGbp] < 0
)

Net Forecast Impact GBP =
SUM ( FactFinanceTransaction[amountGbp] )

High Risk Item Count =
CALCULATE (
    COUNTROWS ( FactFinanceTransaction ),
    FactFinanceTransaction[riskLevel] = "High"
)

Review Item Count =
CALCULATE (
    COUNTROWS ( FactFinanceTransaction ),
    FactFinanceTransaction[status] = "Review"
)

Pending Item Count =
CALCULATE (
    COUNTROWS ( FactFinanceTransaction ),
    FactFinanceTransaction[status] = "Pending"
)

Approved Item Count =
CALCULATE (
    COUNTROWS ( FactFinanceTransaction ),
    FactFinanceTransaction[status] = "Approved"
)

Monthly Budget GBP =
SUM ( DimCostCentre[monthlyBudgetGbp] )

Variance to Monthly Budget GBP =
[Total Spend GBP] - [Monthly Budget GBP]

Working Capital Exposure GBP =
CALCULATE (
    SUM ( FactFinanceTransaction[amountGbp] ),
    FactFinanceTransaction[isWorkingCapitalImpact] = TRUE ()
)

Capex Item Count =
CALCULATE (
    COUNTROWS ( FactFinanceTransaction ),
    FactFinanceTransaction[isCapex] = TRUE ()
)
```

Format GBP measures as currency and count measures as whole numbers.

> Presenter note:
> These measures are the reason the semantic model matters. The app and any report can reuse the same finance definitions.

## Step 11 - Refresh and validate the semantic model

Refresh the semantic model.

Validate:

- transaction rows are visible
- relationships work
- measures return values
- cost-centre filters affect fact measures

> CHECK BEFORE DELIVERY:
> SQL imports do not automatically change Import-mode model results. Refresh the semantic model after importing CSV data.

## Step 12 - Configure the app semantic model alias

Create or update:

```text
fabric.yaml
```

Configure an alias:

```yaml
semanticModels:
  finance:
    workspace: Renishaw-FabricApps
    item: Renishaw Finance Control Semantic Model
```

> CHECK BEFORE DELIVERY:
> Use the exact alias syntax supported by your Rayfin/Fabric Apps SDK version. The audited app used alias `finance`.

## Step 13 - Build the frontend app

The frontend should not look like a Power BI report. It should look like an operational finance review application.

Create these sections:

| Section | Source | Purpose |
| --- | --- | --- |
| Finance Command Centre | DAX measures | Show governed KPI cards. |
| Prioritised Review Queue | DAX transaction query + TypeScript priority score | Show items needing action. |
| Cost Centre Focus | DAX cost-centre query | Let users focus by cost centre. |
| Review Drawer | Semantic row + Rayfin workflow state | Capture owner, note, status, next action, escalation. |
| Why this is not just a report | Static explanatory panel | Make the demo message explicit. |

KPI cards should include:

- Total Spend GBP
- Net Forecast Impact GBP
- High Risk Item Count
- Review Item Count
- Working Capital Exposure GBP
- Capex Item Count

The review queue should show:

- Transaction ID
- Cost centre
- Supplier
- Category
- Amount
- Risk level
- Status
- Due date
- Variance driver
- Suggested action

## Step 14 - Query the semantic model from the app

Use the Fabric Apps semantic-model query path supported by your SDK.

The audited app used:

- `SemanticModelMessageClient`
- `EmbedFabricApiProxy`
- `FabricClient`
- three DAX queries:
  - KPI measures
  - transaction rows
  - cost-centre summaries

> CHECK BEFORE DELIVERY:
> If your SDK version uses different classes, follow the generated examples from your project. Do not fake semantic-model results silently for the customer demo.

The important behavior is:

```text
React app -> semantic model alias finance -> DAX query -> governed result set
```

## Step 15 - Add Rayfin workflow write-back

Use `FinanceReviewAction` for app-owned workflow state.

The review drawer should allow:

- assign owner
- update review status
- save finance note
- set next action
- mark escalation flag

These actions write only to:

```text
FinanceReviewAction
```

They must not update:

- the semantic model
- the source fact table
- source dimensions

> Say this:
> "The semantic model remains the trusted analytical layer. Rayfin stores what finance decides to do next."

## Step 16 - Deploy the Finance App

Run:

```bash
npx rayfin up
```

Then:

1. Open Fabric.
1. Open the `Renishaw-FabricApps` workspace.
1. Open `FinanceApp`.
1. Open the App URL.
1. Confirm KPI cards load.
1. Confirm the review queue loads.
1. Save a review action.
1. Refresh the app and confirm the review action persists.

## Validation checks

Before the customer demo:

| Check | Expected result |
| --- | --- |
| SQL source tables populated | CSV rows imported successfully. |
| Semantic model refreshed | KPI cards show values. |
| Review queue visible | Transactions appear in the app. |
| Cost-centre focus works | Clicking a cost centre filters/focuses the queue. |
| Review write-back works | Notes/status persist after refresh. |
| App opens from Fabric URL | Semantic-model proxy works. |

## Troubleshooting

| Problem | Likely cause | Fix |
| --- | --- | --- |
| SQL tables are empty | CSV import not run | Run `npm run import:finance-csv`. |
| CSV import duplicates rows | Missing key checks | Check by business keys before insert. |
| Semantic model has no rows | Model not connected to SQL or not refreshed | Check source tables and refresh the model. |
| KPI cards fail | Semantic alias or Fabric iframe issue | Open deployed Fabric App URL and check `fabric.yaml`. |
| Queue is empty | Semantic query/filter issue | Confirm DAX query returns transaction rows. |
| Review action does not persist | Rayfin write-back issue | Check `FinanceReviewAction` entity and API connectivity. |
| App works locally but not in Fabric | Deployment/config issue | Re-run `npx rayfin up` and check app item settings. |

## What the finance user is meant to do

The finance user:

1. Opens the Finance App.
1. Reviews governed finance KPIs.
1. Filters or searches the priority queue.
1. Clicks a cost centre to focus the worklist.
1. Opens a transaction.
1. Assigns an owner.
1. Adds a finance note.
1. Sets a next action.
1. Marks the item reviewed or escalated.

This is the key difference from a report: the user acts on the insight and the app stores the action.

## How this differs from Renishaw Finance Control Tower

| Area | Renishaw Finance Control Tower | Finance App |
| --- | --- | --- |
| Source pattern | SQL directly to app | SQL to semantic model to app |
| Measures | React calculations | Governed semantic-model measures |
| Data volume | Small operational dataset | Star-schema analytical dataset |
| Write-back | No end-user write-back | Review actions persisted in SQL |
| Best message | Simple operational app | Governed analytics plus workflow |

## Final summary - what you have built

You have built a Fabric App that uses:

- CSV source files in the app `data` folder
- SQL import into the Fabric Apps managed database
- a semantic model over the SQL source tables
- DAX-backed analytical UI
- Rayfin-managed workflow write-back

> Say this:
> "The Finance App shows why Fabric Apps are different from reports. The semantic model gives finance trusted metrics. Rayfin turns those metrics into an operational review workflow with write-back."
