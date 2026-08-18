# Demo talk track - Renishaw Fabric Apps with Rayfin

Use this as a presenter script, not as a build guide. The aim is to explain the two apps in a way that makes sense to people who are new to Fabric Apps.

The main story is:

> The first app is a direct SQL operational app. The second app is a semantic-model-backed analytical app with workflow write-back.

## **Step 1 - Introduce the two-app story**

### **What to click**

- Start in the `Renishaw-FabricApps` Fabric workspace, but do not open either app immediately.
- If both apps are visible in the workspace, point to `Renishaw Finance Control Tower` first and `FinanceApp` second.

### **What is being shown**

You are showing that the demo contains two Fabric Apps, not one app with two screens. Each app demonstrates a different architecture pattern.

### **What to say**

> "Before I open the apps, I want to explain why I am showing two of them. These are not just two different demos with different layouts. They show two different ways to build a Fabric App with Rayfin."
>
> "The first app is called the Renishaw Finance Control Tower. This is the simpler pattern. It is a direct SQL Fabric App. The app reads finance transactions and budget targets from a Fabric Apps managed SQL database, shows that data in a React user interface, and uses Rayfin's generated data access layer to read and write data."
>
> "The second app is called FinanceApp. This one is different because its analytical data comes from a semantic model. The semantic model contains the governed finance logic: measures, relationships, calculations, KPIs, and business definitions. The app then adds a workflow experience on top, such as review queues, notes, statuses, owners, and next actions."
>
> "So the important distinction is this: in the first app, SQL is the centre of the solution. It is the operational store and the source for the app. In the second app, the semantic model is the source for analytics, and SQL is used for app storage and workflow write-back."
>
> "That distinction matters for finance because not every finance application has the same data need. Sometimes the app owns a small operational process and SQL is enough. Other times the app needs to reuse trusted finance measures that already exist in a semantic model. The value of Fabric Apps is that both patterns can sit inside Fabric rather than requiring a completely separate custom application stack."

### **Summary**

App 1 is direct SQL operational data, while App 2 is governed semantic-model analytics with workflow write-back.

## **Step 2 - Open App 1: Renishaw Finance Control Tower**

### **What to click**

- Open the `Renishaw Finance Control Tower` Fabric App item.
- Open the app URL.
- Pause on the landing page.

### **What is being shown**

You are showing the first app: a focused finance control tower that reads app-owned operational data from the Fabric Apps SQL database.

### **What to say**

> "This is the first app, the Renishaw Finance Control Tower. I would describe this as a lightweight operational finance application. It is not intended to be a full finance system, and it is not intended to replace Power BI. It is designed for a focused operational process: helping finance users review control items and decide what needs attention."
>
> "The data here is synthetic, but the business pattern is realistic. A finance business partner might need to review supplier spend, high-risk invoices, accruals awaiting review, capex threshold items, working-capital exposure, or cost-centre budget pressure. Those are not just numbers on a report. They are items that need someone to look at them, understand the context, and decide what action should happen next."
>
> "The reason this is a Fabric App rather than just a dashboard is that it is designed like an application. It has a user journey. The user starts with a summary, moves into a queue of items, clicks into detail, and can work through finance exceptions in a structured way."
>
> "Rayfin is what helps us build this. Rayfin gives the developer a local project, a command-line workflow, entity definitions, generated data access, and deployment into Fabric. Fabric Apps is the Fabric item that hosts the final running application. So Rayfin is the builder experience; the Fabric App is the thing users open in the Fabric workspace."
>
> "Finance should care about this because it gives teams a route between spreadsheets and full bespoke applications. If a process is too important for a spreadsheet but not big enough to justify a traditional application project, this pattern gives a faster way to create an interactive app that is still close to Fabric data and governance."

### **Summary**

The Finance Control Tower is a focused operational finance app built with Rayfin and hosted as a Fabric App.

## **Step 3 - Walk through the App 1 visuals**

### **What to click**

- Point to the KPI cards at the top.
- Point to the Risk Queue or transaction list.
- Click a high-risk or review item.
- Point to the transaction detail area.
- Point to the cost-centre or budget summary area.

### **What is being shown**

You are showing how the app turns SQL-backed finance records into an operational review experience.

### **What to say**

> "The first thing the app does is answer a very practical finance question: what needs attention? The KPI cards at the top summarise the finance control position. They give the user an immediate view of the overall situation, such as total spend, high-risk items, and items that need review."
>
> "Underneath that, the Risk Queue turns the data into a worklist. This is important because finance teams do not just need another place to look at data. They need help deciding where to focus their time. A queue helps prioritise the records that look most important, risky, overdue, or close to a budget threshold."
>
> "When I click an item, the detail panel updates. This is where the app gives the user context: supplier, category, amount, due date, variance driver, owner, and commentary. This context is what allows a finance user to understand why the item matters before challenging it, approving it, following it up, or escalating it."
>
> "The cost-centre or budget summary connects the individual transaction back to management accountability. Finance conversations often happen at both levels. At the transaction level, someone asks, 'Why is this item here?' At the cost-centre level, someone asks, 'What does this mean for the budget owner or month-end position?' This app supports both views in one place."
>
> "From a beginner Fabric Apps point of view, what you are seeing is a React frontend. React is a frontend framework for building reusable pieces of user interface. Instead of writing one long static page, the developer builds components such as KPI cards, tables, filters, detail panels, and forms. Those components live in the `src` folder of the project."
>
> "The app files often use `.tsx`. TSX means TypeScript XML. It allows the developer to write interface markup that looks a bit like HTML, alongside TypeScript logic that controls the behaviour. That is how we get an app-like experience rather than just a static visual."
>
> "Finance should care because this design helps people work through exceptions. It reduces the need to export data into Excel, manually filter rows, email screenshots, and track follow-ups separately."

### **Summary**

The App 1 visuals guide finance users from summary risk to transaction context and cost-centre ownership.

## **Step 4 - Demo or explain App 1 write-back**

### **What to click**

- If the App 1 version has editable fields, select a transaction, change a review status or note, and save.
- If App 1 is read-only, point to where the editable workflow would fit and explain that App 2 will show the richer write-back example.

### **What is being shown**

You are showing the application concept of write-back: users can create or update operational data, not only view it.

### **What to say**

> "The concept I want to introduce here is write-back. In a traditional report, the user usually consumes data. They might filter, drill, or export, but the report itself is normally not where the user records a decision. In an app, the user can also create or update data."
>
> "For a finance process, write-back might mean adding a note, assigning an item to an owner, changing a review status, recording a next action, or marking something for escalation. That is what changes the experience from 'I saw a problem' to 'I did something about the problem'."
>
> "The browser does not write directly to the SQL database. That would be a poor architecture and would be difficult to secure. Instead, the React screen captures the user's input and calls the Rayfin-generated data layer. Conceptually, the path is: React screen, typed Rayfin data client or GraphQL request, Fabric API layer, and then the SQL database."
>
> "GraphQL is an API style where the frontend asks for the shape of data it needs. In Rayfin, the important point is that much of this plumbing is generated from the data model. The developer defines business entities, registers them in a schema, and Rayfin exposes those entities through generated APIs and typed client access."
>
> "That matters because normally, if you were building a custom app, you would need to design the database, build APIs, handle authentication, write client access code, deploy the backend, and host the frontend. Rayfin and Fabric Apps reduce a lot of that repeated plumbing."

```text
React screen
    |
    v
Rayfin typed data client / GraphQL request
    |
    v
Fabric API layer
    |
    v
Fabric Apps SQL database
```

> "Finance should care because write-back creates accountability. It means the app can capture not just the financial fact, but the business response to that fact."

### **Summary**

Write-back lets finance users save decisions and actions through Rayfin into the Fabric Apps SQL database.

## **Step 5 - Show how App 1 was built in the local project**

### **What to click**

- Open the local `renishaw-finance-control-tower` project folder.
- Point to `package.json`.
- Point to `rayfin.yml`.
- Point to the entity/data folder.
- Point to `data/schema.ts`.
- Point to `src`.
- Point to any deployment-related files or scripts.

### **What is being shown**

You are showing the app's local build structure: configuration, data model, schema, frontend code, and deployment workflow.

### **What to say**

> "Now I want to connect what we saw in the browser to how it was built. This local folder is the developer project. The developer builds here first, tests locally, and then deploys the app into Fabric."
>
> "`package.json` is the Node project file. For beginners, I would describe it as the command centre for the project. It lists packages the app depends on and defines scripts the developer can run, such as starting the local development server, building the app, seeding data, or deploying. When you see commands like `npm run dev`, they are usually defined in `package.json`."
>
> "`rayfin.yml` tells Rayfin how to build and deploy the application infrastructure. It contains the Rayfin and Fabric App configuration: which app this project belongs to, what services need to be deployed, authentication settings, deployment settings, and database configuration. You can think of it as the instruction file that tells Rayfin how this local code maps to the Fabric App running in the workspace."
>
> "The entity files are where the data model starts. A data entity is a business object the application wants to store. In this app, examples are finance transactions and budget targets. The developer defines those entities once in TypeScript. Rayfin can then use those definitions as a blueprint to generate the database schema and API access."
>
> "`data/schema.ts` is where the entities are registered together. This is a really important concept. One entity describes one object, but the schema describes the full application data model. Without schema registration, Fabric would see separate TypeScript files but would not know that they belong together as one database design."
>
> "The `src` folder is the frontend. That is where the React screens, services, and components live. The components control what users see. The services control how the app asks for data or saves updates. So when the user clicks a row or saves a note, the React code in `src` is what handles that interaction."
>
> "The deployment files and scripts are what move the project from local development into Fabric. During deployment, Rayfin packages the app and Fabric provisions the app item and supporting services."
>
> "Finance should care about this build structure because it shows the app is not a disconnected prototype. The business data model, database, API layer, frontend, and deployment configuration are all part of one governed application project."

### **Summary**

The App 1 project combines configuration, entities, schema, React frontend code, and deployment scripts into one Rayfin app.

## **Step 6 - Explain entities, schema, SQL, and generated APIs**

### **What to click**

- Open one entity file, such as `FinanceTransaction.ts` or `BudgetTarget.ts`.
- Open `data/schema.ts`.
- If useful, point back to `src` to show the frontend consumes these definitions through generated access.

### **What is being shown**

You are showing how Rayfin turns business definitions in code into database structures and API access.

### **What to say**

> "This is the part that makes Rayfin useful, so I want to slow down slightly. A data entity is simply a business object. If the business says, 'we need to track finance transactions', that becomes a `FinanceTransaction` entity. If the business says, 'we need to track budget targets', that becomes a `BudgetTarget` entity."
>
> "Inside each entity, the developer defines the fields that object needs. A finance transaction might have an ID, transaction reference, supplier, category, amount, cost centre, due date, risk rating, owner, and status. A budget target might have a cost centre, monthly budget, tolerance, or owner. These fields are written in TypeScript, but they describe the future database structure."
>
> "The schema then brings the entities together. The schema is the complete description of the application's data model. I would describe it as the application's database blueprint. It tells Rayfin: these are the business objects that belong to this app, and this is the structure that should exist behind the scenes."
>
> "When the schema is applied, Rayfin can generate or update the SQL database structure. An entity becomes a table. A field becomes a column. Relationships can become database relationships. This means the developer starts by describing the business model, and Rayfin helps generate the technical plumbing from that model."
>
> "The app also needs a way for the frontend to talk to the backend. That is where the generated API layer comes in. Fabric Apps can expose the entities through generated GraphQL-style APIs and typed data access. The frontend does not need to manually connect to the SQL database. Instead, React calls the generated client, the API layer handles the request, and the SQL database returns or updates the records."
>
> "For beginners, the simplest architecture is this: the React screen is what the user sees, the generated API is the bridge, and the SQL database is where the records live."

```text
React UI
    |
    v
Generated GraphQL/API layer
    |
    v
SQL database tables generated from entities
```

> "Finance should care because this makes custom finance apps more practical. The team can model finance objects such as transactions, targets, forecasts, comments, and approvals without hand-building every backend service from scratch."

### **Summary**

Entities describe business objects, the schema turns them into a complete data model, and Rayfin generates SQL-backed API access from that model.

## **Step 7 - Show the SQL database behind App 1**

### **What to click**

- Return to Fabric.
- Open the SQL database child service or SQL endpoint for the first app.
- Show the transaction and budget target tables.
- If available, show a simple preview or query result.

### **What is being shown**

You are showing the physical SQL data store that supports the first app.

### **What to say**

> "This is the database side of the same application. I want to show this because Fabric Apps are not just pretty frontends. The app has structured data behind it, and in this first pattern, that data lives in the Fabric Apps managed SQL database."
>
> "The records we saw in the Finance Control Tower live here as SQL tables. The app is not reading an embedded spreadsheet or a static JSON file. It is reading structured operational records from SQL through the Rayfin data layer."
>
> "For App 1, this SQL database is both the system of record and the analytics source. That is why I call it the direct SQL pattern. The app owns the data, the app reads the data, and any write-back also goes to this app-owned SQL database."
>
> "This is a good fit for smaller operational finance processes where the app itself is responsible for the data. For example, if a finance team wants a controlled way to track budget review items, adjustment requests, review notes, or approval statuses, the app-owned SQL database gives that information a proper home inside Fabric."
>
> "Finance should care because this avoids the common problem where actions are tracked outside the data platform. If notes, statuses, and owners live in separate spreadsheets or email threads, it becomes difficult to audit decisions. With this pattern, the operational record stays close to the app and close to Fabric governance."

### **Summary**

In App 1, the Fabric Apps SQL database is the system of record and the app reads and writes through Rayfin.

## **Step 8 - Transition to App 2 and state the architecture change**

### **What to click**

- Return to the `Renishaw-FabricApps` workspace.
- Point to `FinanceApp`.
- Open the app.

### **What is being shown**

You are moving from the direct SQL app to the semantic-model-backed app.

### **What to say**

> "Now I am moving to the second app, and this is where the architecture changes. The first app used SQL as the centre of the solution. SQL was the system of record and the app's direct data source."
>
> "This second app uses a different pattern. Here, the semantic model is the source for analytics. The SQL database still exists, but it plays a different role. It stores source data and app workflow data, while the semantic model provides the governed finance measures and calculations."
>
> "That difference is important because many finance teams already have trusted definitions in semantic models. They might already have measures for total spend, variance, overdue exposure, working-capital impact, forecast impact, or margin. If we build a new app and calculate all of those measures separately in React, we risk creating different versions of the truth."
>
> "By using the semantic model, the app can reuse governed finance logic. That means the same definitions can support Power BI reports, Excel, Copilot-style experiences, and this Fabric App."
>
> "Finance should care because this reduces reconciliation problems. Instead of arguing about why the app says one number and the report says another, the app can rely on the same semantic definitions used elsewhere."

### **Summary**

App 2 changes the architecture by using the semantic model for analytics and SQL for app storage and workflow.

## **Step 9 - Walk through App 2 visuals and features**

### **What to click**

- Point to the KPI cards.
- Point to the review queue.
- Use filters if helpful.
- Click a transaction.
- Open the review drawer.
- Point to owner, status, note, next action, and escalation fields.

### **What is being shown**

You are showing a richer finance app that combines governed analytical context with operational workflow.

### **What to say**

> "This second app looks more analytical because it is built around the semantic model. The KPI cards are not just local frontend calculations. They represent governed measures that can come from the semantic model."
>
> "The semantic model is best thought of as the business definition layer. It sits above source data and defines relationships, measures, KPIs, and business-friendly concepts. So instead of every app calculating finance logic in a different way, the model can centralise definitions such as total spend, overdue amount, forecast impact, risk exposure, or variance."
>
> "The review queue then turns those analytical results into a prioritised worklist. This is the application layer. A report might show which transactions are risky, but this app helps the user work through them. The queue gives finance users a practical way to prioritise limited review time."
>
> "When I open the review drawer, there are two types of information. The top part is analytical context: transaction amount, supplier, cost centre, due date, category, variance, and risk indicators. That context comes from the governed data model. The lower part is workflow: owner, status, note, next action, and escalation. That is app-owned state."
>
> "This separation is deliberate. We do not want to write user notes and review statuses into the semantic model. The semantic model should stay focused on governed analytics. The app database stores what the user decides to do next."
>
> "Finance should care because this bridges a major gap. Power BI can show insight, but finance processes often need ownership and follow-up. This app gives the user both: trusted analytical numbers and somewhere to record the action taken."

### **Summary**

App 2 combines semantic-model analytics with an app workflow for reviewing, assigning, noting, and escalating finance items.

## **Step 10 - Demo App 2 write-back**

### **What to click**

- Select a transaction in the review queue.
- Assign the item to yourself if available.
- Change the review status.
- Add a note, for example:

  ```text
  Review with cost centre owner before month-end close.
  ```

- Set a next action if available.
- Save or mark reviewed.

### **What is being shown**

You are showing that App 2 writes operational review state back to Rayfin-managed SQL, not to the semantic model.

### **What to say**

> "This is the clearest example of why the second app is not just a report. I am now saving a finance action. That action might be a status change, an owner assignment, a note, a next action, or an escalation flag."
>
> "The key point is that this write-back is not going into the semantic model. The semantic model is used for analytical reads. It provides trusted measures and business logic. The review action is stored separately in a Rayfin-managed SQL entity, such as `FinanceReviewAction`."
>
> "That entity is app-owned operational state. It records what the finance user did about the analytical insight. For example, the semantic model might tell us an item is high risk or overdue. The app records that Sarah reviewed it, changed the status, added a note, and set the next action."
>
> "So the architecture has two paths. The read path goes from React to the semantic model and returns governed DAX results. The write path goes from React through the Rayfin data client into SQL."

```text
Analytical read path:
React app -> semantic model -> governed DAX results

Workflow write path:
React app -> Rayfin typed data API -> FinanceReviewAction SQL table
```

> "Finance should care because this creates a decision trail. It is no longer just 'the dashboard showed a risk'. It becomes 'the dashboard showed a risk, this person reviewed it, this note was added, this status was chosen, and this action is next'."

### **Summary**

App 2 reads analytics from the semantic model but writes review actions into Rayfin-managed SQL.

## **Step 11 - Show the semantic model behind App 2**

### **What to click**

- Return to the Fabric workspace.
- Open `Renishaw Finance Control Semantic Model`.
- Show the tables.
- Show relationships if available.
- Show measures if available.

### **What is being shown**

You are showing the governed analytical backend that makes the second app different from the first.

### **What to say**

> "This is the semantic model behind the second app. This is the biggest architectural difference from the first app. In the Finance Control Tower, the React app worked directly from app-owned SQL data. In this FinanceApp pattern, the React app uses this semantic model for analytics."
>
> "A semantic model is not just a place where data is stored. It is where data gets business meaning. It defines tables such as cost centres, suppliers, categories, dates, and finance transactions. It defines relationships between those tables, so the model understands how a transaction relates to a supplier, category, date, and cost centre."
>
> "The measures are where a lot of the value sits. Measures are DAX calculations. They can define business metrics such as total spend, overdue amount, forecast impact, variance, working-capital exposure, and risk totals. Because these measures live in the semantic model, they can be reused across multiple experiences."
>
> "That reuse is the reason the app is stronger than if we calculated everything locally. If finance already trusts the semantic model, then the app can use those same definitions rather than creating another version of finance logic."
>
> "The app is therefore not replacing the semantic model. It is using the semantic model as the analytical backend. The app adds the custom experience around it: navigation, queues, forms, notes, statuses, ownership, and write-back."
>
> "Finance should care because this gives both consistency and action. The numbers are governed in the model, and the workflow happens in the app."

### **Summary**

The semantic model is the governed finance definition layer that powers App 2's analytical results.

## **Step 12 - Show how App 2 was built in the local project**

### **What to click**

- Open the local `FinanceApp` project folder.
- Point to `package.json`.
- Point to `fabric.yaml`.
- Point to `rayfin.yml` if present.
- Point to the data/entity files.
- Point to `FinanceReviewAction.ts`.
- Point to `data/schema.ts`.
- Point to `src/services/semanticFinanceClient.ts`.
- Point to `src/services/reviewActionService.ts`.
- Point to `src/components`.
- Point to `src/App.tsx`.

### **What is being shown**

You are showing how the semantic-model-backed app is structured locally, without building it live.

### **What to say**

> "Now I want to show how this second app was built at a project level. I am not going to build it live, but I want to show enough of the folder structure that the architecture makes sense."
>
> "`package.json` is still the project command centre. It controls the scripts for local development, build, import, and deployment. This is where the developer runs commands such as starting the app locally or packaging it for deployment."
>
> "`fabric.yaml` is especially important in this second app. In the first app, the app mainly needed its own SQL-backed data. In this second app, the app also needs to connect to an existing Fabric asset: the semantic model. The `fabric.yaml` file tells the app which workspace and semantic model to use, and what alias the app should use when querying it."
>
> "You can think of `fabric.yaml` as the bridge between the app code and the Fabric estate. The React app cannot magically know which semantic model should power the analytics. The configuration has to say: use this workspace, connect to this semantic model, and expose it to the app under this name."
>
> "The data/entity files still exist because Rayfin still needs to understand the app's SQL-backed data model. In this app, there are analytical source entities, such as cost centre, supplier, category, date, and finance transaction. There is also a workflow entity, such as `FinanceReviewAction`, which stores the user actions created inside the app."
>
> "`data/schema.ts` registers those entities into the application schema. Just like in App 1, schema registration tells Rayfin that these files are part of one complete data model rather than isolated TypeScript classes."
>
> "The `src/services` folder is important because it shows the two data paths. A file like `semanticFinanceClient.ts` is responsible for reading from the semantic model. That keeps DAX queries and semantic-model access in one place instead of scattering them through every component. A file like `reviewActionService.ts` handles write-back through Rayfin into SQL."
>
> "The `src/components` folder contains the user interface building blocks: KPI cards, review queue, cost-centre focus, review drawer, and other visual pieces. `src/App.tsx` brings those pieces together into the full application screen."
>
> "Finance should care because this structure shows the separation of responsibilities. Governed analytics come from the semantic model. User workflow state goes to Rayfin SQL. React provides the experience that joins them together."

### **Summary**

The App 2 project separates semantic-model reads, Rayfin SQL write-back, Fabric configuration, and React user-interface components.

## **Step 13 - Explain the full App 2 build flow**

### **What to click**

- Keep the local project open.
- Optionally show the CSV source folder, semantic model, and app UI in sequence.

### **What is being shown**

You are explaining the build journey from source data to semantic model to custom app.

### **What to say**

> "The build flow for App 2 starts with data. In the lab, the source data begins as CSV files because CSVs are an easy way to provide synthetic demo data. But CSV files are not the final backend. They are imported into structured SQL tables."
>
> "Those SQL tables represent the finance source layer. For example, there are dimension-style tables for cost centres, suppliers, categories, and dates, plus a fact-style table for finance transactions. This gives the solution a more analytical structure than the first app."
>
> "The semantic model then sits above those SQL tables. This is where raw rows become business-ready analytics. Relationships are configured between the tables, and measures are created using DAX. Once that is done, the model can answer finance questions using consistent business logic."
>
> "The Fabric App is then configured to use that semantic model. This is where the Microsoft Data Apps template pattern is relevant. The template is intended for applications whose analytical backend is a semantic model. Instead of only building over app-owned SQL tables, the app can connect to a semantic model and use it as the source of governed analytical results."
>
> "Finally, React provides the custom application experience. The KPI cards and review queue read from the semantic model. The review drawer and action save use Rayfin SQL write-back. The result is a finance application that uses trusted analytics but also lets users record what they are doing about those analytics."

```text
CSV sample data
    |
    v
Fabric Apps SQL source tables
    |
    v
Semantic model with relationships and DAX measures
    |
    v
React analytical app experience
    |
    v
Rayfin SQL write-back for review actions
```

> "Finance should care because this pattern fits how finance teams often work. They need trusted numbers, but they also need process, ownership, and follow-up."

### **Summary**

App 2 is built by loading source data into SQL, modelling it semantically, and adding a React workflow app over the governed measures.

## **Step 14 - Explain why an app is needed if the semantic model already exists**

### **What to click**

- Return to the App 2 UI.
- Point to the review queue, drawer, notes, status, and action controls.

### **What is being shown**

You are explaining the difference between a semantic model, a report, and a Fabric App.

### **What to say**

> "A question people often ask is: if the semantic model already exists, why do we need the app? The answer is that the semantic model provides trusted data and business logic, but it does not provide a complete custom workflow experience on its own."
>
> "A Power BI report is excellent when the user needs to analyse, compare, drill, and understand. But a finance workflow often needs more than that. It needs queues, forms, ownership, comments, status changes, next actions, and sometimes escalation. Those are application behaviours."
>
> "React gives the developer the freedom to design that experience. We are not constrained to a report canvas. We can build navigation, drawers, forms, guided workflows, custom branding, and potentially AI-assisted interactions. The semantic model answers the analytical question; the app controls how the user acts on the answer."
>
> "That is why I would not position Fabric Apps as replacing Power BI. I would position them as extending what finance can do with governed data. If users only need to explore data, a report may be enough. If users need to act, record decisions, and manage a process, an app becomes more appropriate."
>
> "Finance should care because it means analytical insight can stay connected to operational follow-up. Users do not need to leave the governed environment, export the data, and create an unofficial action tracker somewhere else."

### **Summary**

The semantic model supplies trusted metrics, while the Fabric App supplies the custom workflow experience.

## **Step 15 - Repeat the difference between the two apps**

### **What to click**

- Show the Fabric workspace with both app items if possible.
- Or show both app browser tabs.

### **What is being shown**

You are summarising the two architecture patterns side by side.

### **What to say**

> "To bring the two examples together, the Finance Control Tower is the direct SQL pattern. The app owns the operational data. The SQL database is the system of record. Rayfin generates the data access layer. React gives finance users a focused operational screen."
>
> "The FinanceApp is the semantic-model-backed pattern. The semantic model is the source for governed analytics. The app still uses Rayfin, but Rayfin is especially important for the workflow state: notes, statuses, owners, next actions, and escalation decisions."
>
> "So the design decision is not which app looks nicer. The design decision is: what kind of backend does the business process need?"
>
> "If the app owns a small operational process, the direct SQL pattern may be the shortest path. If the app needs governed finance measures that already exist in a semantic model, the semantic-model-backed pattern is stronger."
>
> "Finance should care because this gives a practical decision framework. Not every finance problem needs the same architecture. The pattern should match whether the business is trying to manage operational data, consume governed analytics, or combine analytics with workflow."

| Question | Better fit |
| --- | --- |
| "Do I need a focused app over app-owned operational SQL data?" | App 1 direct SQL pattern |
| "Do I need governed DAX measures, relationships, and reusable KPIs?" | App 2 semantic-model pattern |
| "Do users need to record decisions, notes, ownership, or actions?" | Fabric App with Rayfin write-back |
| "Do users only need to explore data?" | Power BI report may be enough |

### **Summary**

Use direct SQL when the app owns the operational data, and use a semantic model when the app needs governed analytics.

## **Step 16 - End with the Microsoft Data Apps template**

### **What to click**

- Open <https://learn.microsoft.com/en-us/fabric/apps/data-apps-template>.
- Point to the page title and the semantic-model app template guidance.

### **What is being shown**

You are showing that the semantic-model-backed app pattern is aligned with Microsoft's Data Apps template guidance.

### **What to say**

> "I want to end on the Microsoft documentation for the Data Apps template. This is useful because it shows that the second pattern is not just a custom one-off idea. Microsoft provides a template specifically for creating apps connected to a semantic model."
>
> "That is the broader direction this demo is pointing towards. Fabric Apps are not just about putting a nicer frontend over a table. They are about building business applications that sit close to Fabric data, Fabric identity, Fabric governance, and Fabric analytical assets."
>
> "The first app showed the quickest path from SQL-backed operational data to an interactive app. The second app showed how to combine governed semantic-model analytics with workflow and write-back. Together, they show that Fabric Apps can support both operational and analytical finance scenarios."
>
> "The simplest way to remember it is this: Power BI reports help users understand what happened. Fabric Apps built with Rayfin help users work with that information, make decisions, and record the action."
>
> "For finance, that means we are not replacing reporting. We are extending governed data into business processes."

### **Summary**

The Data Apps template reinforces the semantic-model-backed pattern for building custom analytical applications in Fabric.

## Demo readiness checklist

- Both app URLs open successfully from Fabric.
- You are already signed in.
- App 1 SQL rows are seeded.
- App 1 write-back path is tested if the deployed version includes editable fields.
- App 2 SQL source rows are loaded.
- App 2 semantic model has been refreshed.
- App 2 review write-back has been tested.
- The App 2 status filter is understood so the queue does not appear empty.
- The local project folders are already open or pinned for quick access.
- You know which entity, schema, service, and component files you want to point at.
- The Microsoft Learn page is available: <https://learn.microsoft.com/en-us/fabric/apps/data-apps-template>.
- Avoid showing secret-bearing files or tenant-specific credentials.
- Do not overclaim production controls, RLS, external notifications, or real Renishaw financial data.

## Backup lines

| Issue | Say this |
| --- | --- |
| App 1 does not load | "The key pattern is still visible from the SQL child item: this app uses app-owned SQL as the backend and Rayfin generates the access layer." |
| App 1 write-back is not available | "This first app demonstrates the direct SQL data path; the richer write-back demonstration is in the second app." |
| App 2 KPIs do not load | "This usually means the semantic model connection or refresh needs checking. The design is that these cards are backed by semantic-model measures." |
| App 2 queue is empty | "The default filter may be hiding rows, so I will switch to a wider status filter. If it is still empty, the semantic model likely needs a refresh." |
| App 2 save fails | "I will not force the save live. The intended architecture is still that workflow state is stored in the Rayfin-managed SQL entity, separate from the semantic model." |
