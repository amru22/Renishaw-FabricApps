# Demo talk track - Renishaw Fabric Apps with Rayfin

Use this as a 20-minute presenter script. The main story is:

> The first app is a direct SQL operational app. The second app is a semantic-model-backed analytical app with workflow write-back.

## **Step 1 - Introduce the two-app story**

### **What to click**

- Start in the `Renishaw-FabricApps` workspace.
- Point to `Renishaw Finance Control Tower`, then `FinanceApp`.

### **What is being shown**

Two Fabric Apps that demonstrate two different architecture patterns.

### **What to say**

> "I am going to show two Fabric Apps because they demonstrate two different patterns. The first app, Renishaw Finance Control Tower, is the simpler direct SQL pattern. It reads finance transactions and budget targets from a Fabric Apps managed SQL database and presents them in a React app."
>
> "The second app, FinanceApp, is different because its analytics come from a semantic model. The semantic model holds governed measures, relationships, KPIs and business definitions. The app then adds workflow on top: review queues, notes, statuses, owners and next actions."
>
> "So watch for the difference in where the business data comes from. In App 1, SQL is the centre of the solution. In App 2, the semantic model is the analytical source, while SQL stores app workflow and write-back."

### **Summary**

App 1 is direct SQL operational data; App 2 is semantic-model analytics with workflow write-back.

## **Step 2 - Open App 1: Renishaw Finance Control Tower**

### **What to click**

- Open `Renishaw Finance Control Tower`.
- Open the app URL.
- Pause on the landing page.

### **What is being shown**

A focused finance control app that reads app-owned operational data from SQL.

### **What to say**

> "This is the Renishaw Finance Control Tower. It is a lightweight operational finance app, not a full finance system and not just a Power BI report. It helps a finance user review control items and decide what needs attention."
>
> "The data is synthetic, but the pattern is realistic. Finance teams often need to review high-risk invoices, supplier spend, accruals, capex threshold items or cost-centre pressure. These are not just numbers to view; they need ownership and follow-up."
>
> "Rayfin is the development framework used to build this. It gives us the local project, entity definitions, generated data access and deployment workflow. The Fabric App is the item users open inside the Fabric workspace."

### **Summary**

The Finance Control Tower is a Rayfin-built operational finance app hosted as a Fabric App.

## **Step 3 - Walk through the App 1 visuals**

### **What to click**

- Point to the KPI cards.
- Point to the Risk Queue.
- Click a high-risk or review item.
- Point to the detail and cost-centre areas.

### **What is being shown**

SQL-backed finance data presented as an operational review experience.

### **What to say**

> "The KPI cards answer the first finance question: what needs attention? They summarise the control position so the user can quickly see spend, risk and items needing review."
>
> "The Risk Queue turns the data into a worklist. Instead of asking users to scan a spreadsheet, the app prioritises the items that are most likely to need action."
>
> "When I click a row, the detail area gives the business context: supplier, category, amount, owner, due date and reason for attention. The cost-centre view links the individual item back to budget ownership."
>
> "This is built in React. React lets developers create reusable UI components like cards, tables, filters and detail panels. The `.tsx` files combine TypeScript logic with HTML-like screen markup."
>
> "Finance should care because this reduces the need to export data, manually filter rows and track follow-up separately."

### **Summary**

The App 1 visuals guide finance users from summary risk to transaction context and budget ownership.

## **Step 4 - Demo or explain App 1 write-back**

### **What to click**

- If editable fields exist, change a status or note and save.
- If not, point to where workflow would fit and say App 2 shows the richer write-back example.

### **What is being shown**

The app concept of write-back: users can update operational data, not only view it.

### **What to say**

> "The key app concept here is write-back. A report normally lets users read, filter and drill into data. An app can also let users save something back, such as a note, status, owner or next action."
>
> "The browser does not write directly to SQL. React captures the user's input, calls the Rayfin-generated data client or GraphQL API, and that API writes to the Fabric Apps SQL database."

```text
React screen -> Rayfin data client / GraphQL -> Fabric API -> SQL database
```

> "Finance should care because write-back creates accountability. The app can capture not just the financial issue, but what the business decided to do about it."

### **Summary**

Write-back lets finance users save decisions and actions into the Fabric Apps SQL database.

## **Step 5 - Show how App 1 was built in the local project**

### **What to click**

- Open the local `renishaw-finance-control-tower` project.
- Point to `package.json`, `rayfin.yml`, the entity folder, `data/schema.ts`, `src`, and deployment files.

### **What is being shown**

The local Rayfin project structure behind the first app.

### **What to say**

> "`package.json` is the project command centre. It defines scripts for local development, build, seed and deployment."
>
> "`rayfin.yml` tells Rayfin how this local project maps to Fabric: the app, services, authentication, deployment settings and SQL database configuration."
>
> "The entity files define the business objects the app stores, such as finance transactions and budget targets. Instead of starting by manually creating SQL tables, the developer describes these objects in TypeScript."
>
> "`data/schema.ts` registers the entities together. One entity describes one object; the schema describes the full application data model."
>
> "`src` contains the React frontend: the screens, components and services that users interact with."

### **Summary**

The App 1 project combines configuration, entities, schema, React code and deployment settings.

## **Step 6 - Explain entities, schema, SQL and generated APIs**

### **What to click**

- Open an entity file, such as `FinanceTransaction.ts`.
- Open `data/schema.ts`.

### **What is being shown**

How Rayfin turns business definitions into SQL-backed API access.

### **What to say**

> "A data entity is a business object. If finance needs to track transactions, budget targets, forecasts or comments, each concept can become an entity."
>
> "The entity defines the fields that object needs, such as amount, supplier, category, owner, status or due date. Rayfin can use those fields as a blueprint for SQL tables and columns."
>
> "The schema brings the entities together into the complete application data model. Without the schema, the files are just separate TypeScript classes. With the schema, Rayfin knows they form one database design."
>
> "Rayfin then exposes those entities through generated API access, often GraphQL-style. The React frontend calls the generated layer instead of connecting directly to SQL."

```text
React UI -> Generated API layer -> SQL tables from entities
```

### **Summary**

Entities define business objects, the schema registers the model, and Rayfin generates SQL-backed API access.

## **Step 7 - Show the SQL database behind App 1**

### **What to click**

- Return to Fabric.
- Open the SQL database child service or SQL endpoint for App 1.
- Show the transaction and budget target tables.

### **What is being shown**

The physical SQL store that supports the first app.

### **What to say**

> "This is the database behind the app. The records we saw in the Finance Control Tower live here as structured SQL tables."
>
> "For App 1, this SQL database is both the system of record and the data source for the app. That is why I call it the direct SQL pattern."
>
> "This works well when the app owns a focused operational process, such as tracking review items, notes, statuses or budget targets. Finance should care because actions stay in a governed data store instead of being spread across spreadsheets and email threads."

### **Summary**

In App 1, the Fabric Apps SQL database is the system of record.

## **Step 8 - Transition to App 2 and state the architecture change**

### **What to click**

- Return to the workspace.
- Open `FinanceApp`.

### **What is being shown**

The move from direct SQL to a semantic-model-backed app.

### **What to say**

> "Now the architecture changes. In the first app, SQL was the centre. In this second app, the semantic model is the analytical source."
>
> "The SQL database still exists, but its role is different. It stores source data and workflow data. The semantic model provides the governed finance measures and calculations."
>
> "This matters because finance teams often already trust measures in a semantic model, such as total spend, variance, overdue exposure or working-capital impact. If a new app recalculates those separately, we risk creating different versions of the truth."

### **Summary**

App 2 uses the semantic model for analytics and SQL for storage and workflow.

## **Step 9 - Walk through App 2 visuals and features**

### **What to click**

- Point to KPI cards.
- Point to the review queue.
- Use filters if helpful.
- Open a transaction drawer.
- Point to status, owner, note, next action and escalation fields.

### **What is being shown**

A richer finance app that combines governed analytics with workflow.

### **What to say**

> "These KPI cards represent the analytical layer. The app is not just reading raw rows and making up numbers locally; it can query governed measures from the semantic model."
>
> "The review queue turns those analytical results into a worklist. A report can show risk, but this app helps the user work through the risk."
>
> "The drawer shows the split clearly. The analytical context is the transaction, supplier, cost centre, due date and risk. The workflow context is the owner, status, note, next action and escalation."
>
> "Finance should care because this connects trusted numbers to action. Users can review the issue and record what happens next in the same experience."

### **Summary**

App 2 combines semantic-model analytics with a finance workflow for review and follow-up.

## **Step 10 - Demo App 2 write-back**

### **What to click**

- Select a transaction.
- Assign it to yourself if available.
- Change the status.
- Add a note:

  ```text
  Review with cost centre owner before month-end close.
  ```

- Save or mark reviewed.

### **What is being shown**

Workflow state being written to Rayfin-managed SQL, not to the semantic model.

### **What to say**

> "This is the clearest difference from a report. I am saving a finance action, not just viewing a visual."
>
> "The write-back does not go into the semantic model. The semantic model remains the analytical layer. The action is stored in a Rayfin-managed SQL entity such as `FinanceReviewAction`."
>
> "So App 2 has two paths: analytical reads come from the semantic model, while workflow writes go through Rayfin into SQL."

```text
Read:  React app -> semantic model -> DAX results
Write: React app -> Rayfin data API -> FinanceReviewAction SQL table
```

> "Finance should care because this creates a decision trail: who reviewed the item, what status was chosen, what note was added and what happens next."

### **Summary**

App 2 reads analytics from the semantic model but writes review actions into SQL.

## **Step 11 - Show the semantic model behind App 2**

### **What to click**

- Open `Renishaw Finance Control Semantic Model`.
- Show tables, relationships and measures if available.

### **What is being shown**

The governed analytical backend for the second app.

### **What to say**

> "This semantic model is the main difference from App 1. A semantic model is not just storage; it is where data gets business meaning."
>
> "It defines tables such as cost centres, suppliers, categories, dates and transactions. It also defines relationships so the model understands how those tables connect."
>
> "The measures are the key finance definitions. They can calculate total spend, overdue amount, forecast impact, variance or working-capital exposure using DAX."
>
> "Because those measures live in the semantic model, the app can reuse the same trusted definitions as reports and other Fabric experiences."

### **Summary**

The semantic model is the governed finance definition layer for App 2.

## **Step 12 - Show how App 2 was built in the local project**

### **What to click**

- Open the local `FinanceApp` project.
- Point to `package.json`, `fabric.yaml`, entity files, `FinanceReviewAction.ts`, `data/schema.ts`, `src/services`, `src/components`, and `src/App.tsx`.

### **What is being shown**

The project structure for the semantic-model-backed app.

### **What to say**

> "`package.json` still controls local development, build, import and deployment scripts."
>
> "`fabric.yaml` is especially important here because the app must know which Fabric workspace and semantic model to connect to. It is the bridge between the app code and the Fabric assets."
>
> "The entity files define SQL-backed data, including source entities and the write-back entity `FinanceReviewAction`. `data/schema.ts` registers them as one app data model."
>
> "The `src/services` folder shows the two data paths. `semanticFinanceClient.ts` handles semantic-model reads. `reviewActionService.ts` handles Rayfin SQL write-back."
>
> "The components folder contains the visible UI pieces, such as KPI cards, review queue and review drawer."

### **Summary**

The App 2 project separates semantic-model reads, Rayfin write-back, configuration and React components.

## **Step 13 - Explain the full App 2 build flow**

### **What to click**

- Keep the local project open.
- Optionally point to CSV source files, the semantic model and the app UI.

### **What is being shown**

The build journey from source data to semantic model to app workflow.

### **What to say**

> "The build starts with synthetic CSV data, but CSV is not the final backend. The files are imported into SQL tables for cost centres, suppliers, categories, dates and finance transactions."
>
> "A semantic model is then created over those SQL tables. This is where relationships and DAX measures turn raw rows into business-ready analytics."
>
> "The app is configured to connect to that semantic model, following the Data Apps template pattern. React then provides the custom user experience, while Rayfin stores workflow actions in SQL."

```text
CSV data -> SQL source tables -> Semantic model -> React app -> Rayfin SQL write-back
```

### **Summary**

App 2 loads data into SQL, models it semantically, then adds a React workflow app over the governed measures.

## **Step 14 - Explain why an app is needed if the semantic model exists**

### **What to click**

- Return to the App 2 UI.
- Point to queue, drawer, notes, status and action controls.

### **What is being shown**

The difference between a semantic model, a report and an app.

### **What to say**

> "A common question is: if the semantic model already exists, why do we need the app? The answer is that the semantic model provides trusted data and logic, but it does not provide a complete workflow experience by itself."
>
> "A report is excellent for analysis. An app is better when users need queues, forms, notes, owners, statuses and next actions."
>
> "So I would not position Fabric Apps as replacing Power BI. I would position them as extending governed data into business processes."

### **Summary**

The semantic model supplies trusted metrics, while the Fabric App supplies the workflow experience.

## **Step 15 - Repeat the difference between the two apps**

### **What to click**

- Show both app items in the workspace or both browser tabs.

### **What is being shown**

The two architecture patterns side by side.

### **What to say**

> "The Finance Control Tower is the direct SQL pattern. The app owns the operational data, SQL is the system of record, Rayfin generates the access layer and React provides the user experience."
>
> "FinanceApp is the semantic-model-backed pattern. The semantic model provides governed analytics, while Rayfin stores workflow state such as notes, owners, statuses and next actions."
>
> "The design decision is not which app looks nicer. It is what backend the business process needs: app-owned SQL data, governed semantic-model analytics, or both."

| Question | Better fit |
| --- | --- |
| Focused app over app-owned operational SQL data? | App 1 direct SQL pattern |
| Governed DAX measures, relationships and reusable KPIs? | App 2 semantic-model pattern |
| Decisions, notes, ownership or actions? | Fabric App with Rayfin write-back |
| Only data exploration? | Power BI report may be enough |

### **Summary**

Use direct SQL when the app owns operational data, and use a semantic model when the app needs governed analytics.

## **Step 16 - End with the Microsoft Data Apps template**

### **What to click**

- Open <https://learn.microsoft.com/en-us/fabric/apps/data-apps-template>.
- Point to the Data Apps template guidance.

### **What is being shown**

Microsoft's guidance for building apps connected to semantic models.

### **What to say**

> "I want to end on the Microsoft Data Apps template page because it reinforces the second pattern. Microsoft provides a template for creating apps connected to semantic models."
>
> "The first app showed the quickest path from SQL-backed operational data to an interactive app. The second app showed how governed semantic-model analytics can be combined with workflow and write-back."
>
> "The simplest way to remember it is: Power BI reports help users understand what happened. Fabric Apps built with Rayfin help users work with that information, make decisions and record action."

### **Summary**

The Data Apps template reinforces the semantic-model-backed pattern for custom analytical apps in Fabric.

## Demo readiness checklist

- Both app URLs open successfully from Fabric.
- You are already signed in.
- App 1 SQL rows are seeded.
- App 2 semantic model has been refreshed.
- App 2 review write-back has been tested.
- Local project folders are already open or pinned.
- Microsoft Learn page is available: <https://learn.microsoft.com/en-us/fabric/apps/data-apps-template>.
- Avoid showing secret-bearing files or tenant-specific credentials.

## Backup lines

| Issue | Say this |
| --- | --- |
| App 1 does not load | "The key pattern is still visible from the SQL child item: this app uses app-owned SQL as the backend and Rayfin generates the access layer." |
| App 1 write-back is not available | "This first app demonstrates the direct SQL data path; the richer write-back demonstration is in the second app." |
| App 2 KPIs do not load | "This usually means the semantic model connection or refresh needs checking. The design is that these cards are backed by semantic-model measures." |
| App 2 queue is empty | "The default filter may be hiding rows, so I will switch to a wider status filter. If it is still empty, the semantic model likely needs a refresh." |
| App 2 save fails | "I will not force the save live. The intended architecture is still that workflow state is stored in the Rayfin-managed SQL entity, separate from the semantic model." |
