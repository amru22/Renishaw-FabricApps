# Build lab - Finance App

## Lab overview

This lab builds the **Finance App**: a hybrid Fabric App that combines SQL-backed data, a Power BI/Fabric semantic model, DAX queries, and Rayfin workflow write-back.

This is the richer demo app. Use it to show how Fabric Apps built with Rayfin can expose governed analytical data from a semantic model while also capturing operational review actions in app-owned SQL storage.

## Business scenario

Renishaw finance users need more than a dashboard. They need to:

- understand trusted finance KPIs
- identify high-risk or overdue items
- analyse cost-centre budget pressure
- review working-capital exposure
- assign ownership and capture follow-up decisions

The semantic model provides the governed analytical layer. Rayfin provides the application and write-back workflow layer.

> Say this:
> "A Power BI report is excellent for analysis. This Fabric App uses governed analytical measures, but adds the operational workflow finance users need when they must act on the numbers."

## What the app demonstrates

| Capability | What the app shows |
| --- | --- |
| SQL star schema | Source tables for transactions, cost centres, suppliers, categories, and dates. |
| Semantic model | Governed measures for spend, variance, risk, review, and working capital. |
| DAX from React | The app queries the semantic model directly for analytical sections. |
| Rayfin write-back | Review actions are persisted in `FinanceReviewActions`. |
| App versus report | The app adds queue prioritisation, notes, owner assignment, status, and escalation flags. |

## Architecture summary

```text
CSV fixtures
    |
    v
SQL migration
    |
    v
Fabric SQL source tables
    |
    v
Import semantic model
    |
    v
DAX queries through Fabric app host
    |
    v
React finance app

React review drawer
    |
    v
Rayfin typed data API
    |
    v
FinanceReviewActions SQL table
```

Important:

- The semantic model is read-only from the app perspective.
- Review workflow state is stored separately in Rayfin SQL.
- SQL source changes require a semantic model refresh before DAX sees the changes.
- Review action writes do not require a semantic model refresh.

## Prerequisites

Before starting, confirm:

- You have access to the `Renishaw-FabricApps` Fabric workspace.
- Fabric Apps are enabled in the tenant.
- You have permission to deploy Fabric Apps.
- You have permission to deploy or update semantic models.
- Node.js is installed.
- You can authenticate with Rayfin/Fabric CLI tooling.
- You have the `FinanceApp` repository.

> CHECK BEFORE DELIVERY:
> This app deliberately refuses to run outside the Fabric iframe because semantic-model access depends on the Fabric app host. Test it from the deployed Fabric App URL, not only localhost.

## Required repo files

The app should contain these key files or equivalents:

| File or folder | Purpose |
| --- | --- |
| `rayfin.yml` | Fabric Apps backend, auth, SQL entities, and hosting configuration. |
| `fabric.yaml` | Semantic-model alias configuration, including alias `finance`. |
| `data/` | Rayfin entity definitions. |
| `data_app_semantic_model/` | CSV fixtures, migration script, deployment assets, and TMDL model definition. |
| `finance/` | Semantic model / DAX query assets identified in the audit. |
| `components/` | React app components for KPIs, queue, cost centres, and review drawer. |
| `fabric-client.ts` | Fabric semantic-model query client setup. |
| `use-review-actions.ts` | Rayfin read/write hook for review workflow actions. |
| `priority.ts` | Client-side queue priority scoring. |
| `README.md` | Current app runbook. |

> CHECK BEFORE DELIVERY:
> File paths may differ by project structure. Search for `SemanticModelMessageClient`, `FinanceReviewAction`, `priority.ts`, and `fabric.yaml` if needed.

## Required Fabric workspace and capacity assumptions

| Item | Recommended value |
| --- | --- |
| Fabric workspace | `Renishaw-FabricApps` |
| Fabric App item | `FinanceApp` |
| Semantic model | `Renishaw Finance Control Semantic Model` |
| Semantic model alias | `finance` |
| App purpose | Semantic-model-backed finance workflow app |
| Data type | Synthetic finance demo data |

## Required Rayfin/Fabric Apps setup

Open `rayfin.yml` and confirm:

1. Fabric auth is enabled.
1. SQL data service is enabled.
1. Static hosting is enabled.
1. The deployed Fabric App item details are populated after deployment.

Open `fabric.yaml` and confirm:

1. The semantic model alias is `finance`.
1. The alias points to the deployed semantic model.
1. Workspace/model identifiers match the `Renishaw-FabricApps` deployment.

> CHECK BEFORE DELIVERY:
> Hardcoded workspace, database, or semantic-model IDs reduce portability. Confirm they point to your live demo workspace before delivery.

## Required data setup

The app uses five analytical source entities and one writable workflow entity.

### Read-only analytical source entities

| Entity | Purpose |
| --- | --- |
| `DimCategory` | Spend classification. |
| `DimCostCentre` | Finance owner, budget, tolerance, and working-capital priority. |
| `DimDate` | Calendar table. |
| `DimSupplier` | Supplier classification. |
| `FactFinanceTransaction` | Analytical transaction fact table. |

These are seeded from deterministic CSV fixtures and feed the semantic model.

### Writable workflow entity

| Entity | Purpose |
| --- | --- |
| `FinanceReviewAction` | Stores review owner, status, notes, next action, escalation flag, and timestamps. |

> Presenter note:
> This split is central to the demo. Analytics come from the semantic model. Workflow decisions are stored in the app database.

## Step-by-step build instructions

### Step 1 - Open the Finance App repository

In a terminal:

```bash
cd "<path to FinanceApp>"
code .
```

Confirm the app identity:

```bash
dir
```

Look for:

- `rayfin.yml`
- `fabric.yaml`
- `package.json`
- `data_app_semantic_model`
- `components`

### Step 2 - Install dependencies

Run:

```bash
npm install
```

Then run:

```bash
npm run build
```

> CHECK BEFORE DELIVERY:
> If the build script name differs, inspect `package.json` and use the app's actual build script.

### Step 3 - Review the Rayfin entities

Open the `data/` folder.

Confirm that:

1. The five source entities exist.
1. The source entities use read-only authenticated access.
1. `FinanceReviewAction` exists.
1. `FinanceReviewAction` supports authenticated create/update/read.
1. All entities are registered in the Rayfin schema.

> Say this:
> "The app protects the analytical source data as read-only and uses a separate workflow entity for finance review state."

### Step 4 - Apply or deploy the Rayfin SQL schema

Run:

```bash
npx rayfin up db apply
```

If this is a first deployment, run:

```bash
npx rayfin up
```

What to check:

- SQL child service exists.
- The analytical tables exist.
- `FinanceReviewActions` exists.
- The app deploys without schema errors.

### Step 5 - Seed the SQL source tables

Use the migration assets under:

```text
data_app_semantic_model/
```

The audit identified deterministic CSV fixtures and a transactional migration script.

Expected migration script:

```text
migrate-csv-to-sql.mjs
```

Run the package script that calls the migration.

> CHECK BEFORE DELIVERY:
> Inspect `package.json` for the exact command. It may be named differently, for example `npm run migrate`, `npm run seed`, or `npm run data:load`.

After running the migration, validate the SQL source tables contain:

- categories
- cost centres
- dates
- suppliers
- finance transactions

Expected fixture scale from the audit:

| Table | Expected shape |
| --- | --- |
| Categories | around 15 rows |
| Cost centres | around 10 rows |
| Dates | 2026 calendar |
| Suppliers | around 25 rows |
| Transactions | around 210 rows |

### Step 6 - Deploy or refresh the semantic model

Open the semantic model deployment assets under:

```text
data_app_semantic_model/
finance/
```

The semantic model should be:

```text
Renishaw Finance Control Semantic Model
```

Confirm the model is an Import-mode star schema with:

- one transaction fact table
- four dimensions
- relationships from fact to cost centre, supplier, category, and invoice date
- 18 explicit measures

After SQL data is loaded, refresh the semantic model.

> CHECK BEFORE DELIVERY:
> The app's analytical sections will not reflect SQL fixture changes until the Import semantic model refresh completes.

### Step 7 - Validate semantic model measures

Open the semantic model in Fabric or the supported model authoring tool.

Check measure groups:

| Folder | Example measures |
| --- | --- |
| Finance Overview | Total Spend, Forecast Recoveries, Net Forecast Impact |
| Risk and Review | High Risk Item Count, Review Item Count, Pending Item Count |
| Budget and Variance | Monthly Budget, Variance to Monthly Budget |
| Working Capital | Working Capital Exposure, Overdue Amount, Due in Next 14 Days |

> Presenter note:
> Do not describe these as React calculations. In this app, these are semantic-model measures queried by DAX.

### Step 8 - Review semantic-model query code

Search for:

```text
SemanticModelMessageClient
EmbedFabricApiProxy
FabricClient
```

Open the semantic model client file, identified in the audit as:

```text
fabric-client.ts
```

Confirm the app issues DAX queries for:

1. KPI measures
1. denormalised transaction rows
1. cost-centre summary rows

> Say this:
> "The app is not embedding a Power BI report. It is querying the governed semantic model and using those results inside a custom finance workflow."

### Step 9 - Review the frontend components

Open the React components folder.

Confirm the app has:

- KPI cards
- prioritised review queue
- search and filters
- cost-centre summary/focus
- architecture explainer
- transaction review drawer
- theme toggle

Open:

```text
priority.ts
```

Confirm queue ranking is client-side logic using:

- risk
- source status
- due date
- value
- capex flag
- working-capital impact flag
- workflow status

> Presenter note:
> Priority score is app logic, not a semantic-model measure.

### Step 10 - Review workflow write-back

Open:

```text
use-review-actions.ts
```

Confirm the app:

1. Reads existing `FinanceReviewAction` rows.
1. Matches actions to transactions by `transactionId`.
1. Creates or updates review rows.
1. Does not write back to the semantic model.
1. Does not update source transaction rows.

Workflow fields include:

- assigned owner
- review status
- finance note
- next action
- escalation flag
- updated timestamp

> Say this:
> "The semantic model remains the trusted analytical layer. Rayfin captures what finance decides to do next."

### Step 11 - Deploy the app

Run:

```bash
npx rayfin up
```

After deployment:

1. Open the Fabric portal.
1. Open the `Renishaw-FabricApps` workspace.
1. Open the `FinanceApp` Fabric App item.
1. Copy/open the App URL.
1. Confirm the app opens inside Fabric.

> CHECK BEFORE DELIVERY:
> This app may not run correctly outside Fabric because semantic-model proxy access depends on the Fabric iframe host.

### Step 12 - Validate the customer demo flow

In the deployed app:

1. Confirm KPI cards load.
1. Confirm review queue loads.
1. Change status filter to `Any` if the default hides approved items.
1. Search for a supplier or cost centre.
1. Click a cost centre and confirm the queue focuses.
1. Open a transaction review drawer.
1. Assign the item to yourself.
1. Add a short note.
1. Toggle escalation if appropriate.
1. Save.
1. Refresh the page and confirm the saved review action persists.

> CHECK BEFORE DELIVERY:
> The audit found the fallback is in-memory, not durable session storage. Make sure Rayfin write-back is live before using save actions in the customer demo.

## Semantic model setup/configuration explanation

The semantic model exists to centralise finance logic.

Instead of each app or report calculating spend, variance, risk counts, and working-capital exposure independently, the semantic model provides reusable measures.

Finance questions supported:

- What is total spend?
- What is the net forecast impact?
- Which items are high risk?
- Which items are still in review?
- Which cost centres are above budget?
- What amount is overdue?
- What is due in the next 14 days?
- Which items create working-capital exposure?

## How SQL feeds the semantic model

1. CSV fixtures are loaded into SQL source tables.
1. The semantic model imports those SQL tables.
1. Relationships define the star schema.
1. Measures calculate finance KPIs.
1. The app queries the model with DAX.

If SQL source rows change, refresh the semantic model before expecting the DAX-backed app sections to change.

## How this differs from Renishaw Finance Control Tower

| Area | Renishaw Finance Control Tower | Finance App |
| --- | --- | --- |
| Data read path | SQL directly through Rayfin typed client | SQL into semantic model, then DAX |
| Calculations | React calculations | Semantic-model measures |
| Data volume | Small seed set | Larger star-schema fixture set |
| Write-back | No end-user write-back | Review workflow writes to `FinanceReviewActions` |
| Best demo point | Simplicity of Fabric App + SQL | Governed analytics plus operational action |

## Troubleshooting

| Problem | Likely cause | Fix |
| --- | --- | --- |
| KPI cards do not load | App not running inside Fabric iframe or semantic alias issue | Open deployed Fabric App URL and check `fabric.yaml`. |
| Queue is empty | Default filter hides rows or semantic model not refreshed | Switch status to `Any`; refresh semantic model. |
| SQL rows loaded but app analytics unchanged | Import semantic model has stale data | Refresh the semantic model. |
| Save action does not persist | Rayfin write-back unavailable | Check `FinanceReviewActions` entity and Rayfin API connectivity. |
| Saved actions disappear after refresh | App fell back to in-memory state | Fix Rayfin write-back before customer demo. |
| Escalation message overclaims automation | App only stores escalation flag | Say "marked for escalation," not "notification sent." |
| Supplier field labelled incorrectly | Audit found a supplier type displayed as "Country" | Say "supplier type" or fix label before delivery. |

## Final summary - what you have built

You have built a hybrid Fabric App that demonstrates:

- SQL source data
- a governed Import-mode semantic model
- DAX-backed analytical UI
- Rayfin app-owned workflow write-back
- finance review actions that persist in SQL

Use this app to explain the advanced pattern:

> Say this:
> "This app shows how Fabric Apps can sit between governed analytics and operational process. The semantic model explains the numbers; Rayfin lets finance users act on them."

