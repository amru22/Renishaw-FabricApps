# Demo talk track - Renishaw Fabric Apps with Rayfin

## Demo overview

| Item | Detail |
| --- | --- |
| Audience | Renishaw finance team |
| Demo goal | Show how Fabric Apps built with Rayfin support interactive finance applications, not just dashboards. |
| Key message | Power BI reports explain the data; Fabric Apps built with Rayfin help finance teams act on the data. |
| Time available | 15-20 minutes |
| Apps | `Renishaw Finance Control Tower` and `FinanceApp` |

## What not to over-explain

Do not spend time on:

- live deployment
- npm install output
- model deployment internals
- authentication setup
- SQL migration details
- semantic model authoring details
- waiting for refresh

Show the finished apps and explain the architecture only enough to support the business story.

## Transition from presentation

> Say this:
> "We have just covered the distinction between Rayfin and Fabric Apps. Rayfin is the SDK, CLI, and development framework. The Fabric App is the deployed item running inside Microsoft Fabric. Now I want to make that practical with two finance examples. The first app shows the simplest pattern: a Fabric App reading directly from its managed SQL database. The second app extends the pattern by using a semantic model for governed finance metrics and Rayfin for workflow write-back."

Then say:

> "The data is synthetic and illustrative. The point is the pattern: finance users need trusted numbers, but they also need an operational way to review, assign, comment, and follow up."

## Demo timing plan

| Time | Section | Goal |
| --- | --- | --- |
| 0:00-2:00 | Setup and context | Frame the two-app story. |
| 2:00-8:00 | App 1: Renishaw Finance Control Tower | Show direct SQL-backed Fabric App pattern. |
| 8:00-15:00 | App 2: Finance App | Show semantic-model-backed analytics plus Rayfin workflow. |
| 15:00-18:00 | Comparison and close | Explain the difference in finance-friendly language. |

If time is tight, reduce App 1 to three minutes and spend more time on App 2.

## Demo script for App 1 - Renishaw Finance Control Tower

### Step 1 - Open the Fabric workspace

**What to click**

1. Open Fabric.
1. Go to workspace `Renishaw-FabricApps`.
1. Click the `Renishaw Finance Control Tower` Fabric App item.
1. Open the App URL.

**What the audience sees**

The app opens to a finance dashboard with KPI cards, a Risk Queue, transaction detail, and cost-centre summary.

**What to say**

> "This first app is the direct SQL pattern. Rayfin defines the finance entities, Fabric Apps provides the managed SQL backend and generated API, and the React frontend presents the finance workflow."

**Why finance should care**

This is useful when a finance process is too structured for a spreadsheet but does not need a full enterprise application build.

**Backup line if something does not load**

> "If the app is still loading, the key point is that this version reads directly from the Fabric App SQL backend. I can show the same pattern from the SQL child item while the app refreshes."

### Step 2 - Show the KPI cards

**What to click**

No click required. Point to the top KPI cards.

**What the audience sees**

Cards for total spend, high-risk items, and items in review.

**What to say**

> "The app starts with a very finance-friendly question: what needs attention? It is not trying to be a full Power BI report. It is a focused operational screen."

**Why finance should care**

Finance users need a fast way to identify exceptions, not another spreadsheet to scan.

### Step 3 - Use the Risk Queue

**What to click**

Click a row in the Risk Queue, preferably a high-risk or review item.

**What the audience sees**

The selected transaction detail panel updates.

**What to say**

> "Selecting a row gives the finance user the business context: supplier, category, amount, due date, variance driver, owner, and commentary."

**Why finance should care**

The user can understand the reason for the spend before challenging, approving, or following up.

**Backup line if row selection fails**

> "The intended interaction is simple: choose a finance item and review its context. For the session, the important point is that this is app-style interaction over SQL-backed data."

### Step 4 - Show Cost Centre Summary

**What to click**

Scroll or point to the Cost Centre Summary.

**What the audience sees**

Cost-centre spend compared with budget targets.

**What to say**

> "This section combines transactions with budget targets. The app calculates a simple variance view in React from the SQL data."

**Why finance should care**

It connects individual exceptions to budget ownership.

**Important caveat**

> Say this:
> "This is a synthetic snapshot, not formal period accounting."

### Step 5 - Explain the architecture simply

**What to click**

Optionally switch to the Fabric App item and show the SQL child service.

**What the audience sees**

The Fabric App item and SQL child item.

**What to say**

> "This app reads from the Fabric Apps managed SQL database using the Rayfin typed data client. There is no semantic model in this first app. It is the simplest operational app pattern."

**Why finance should care**

It demonstrates a fast route from controlled data to a usable finance review screen.

## Demo script for App 2 - Finance App

### Step 1 - Transition to the second app

**What to click**

Return to the `Renishaw-FabricApps` workspace and open `FinanceApp`.

**What the audience sees**

The richer finance app with KPI cards, review queue, cost-centre summary, explainer, and review drawer.

**What to say**

> "The second app extends the pattern. Here, the finance source data feeds a semantic model. The app queries governed measures from that model, but Rayfin still provides the application workflow and write-back."

**Why finance should care**

This connects trusted analytics with operational follow-up.

### Step 2 - Show governed KPI cards

**What to click**

Point to the KPI cards.

**What the audience sees**

KPI cards such as spend, forecast impact, risk, review, working capital, overdue, and due-next-14-days metrics.

**What to say**

> "These metrics are not just local React calculations. They come from the semantic model. That matters because the same definitions can be reused across reports, apps, Excel, and other Fabric experiences."

**How to explain the semantic model**

> "Think of the semantic model as the finance definition layer. It standardises measures like total spend, variance, overdue amount, and working-capital exposure so different tools do not calculate them differently."

**Why finance should care**

Trusted definitions reduce reconciliation arguments and make app decisions easier to defend.

**Backup line if KPIs do not load**

> "If these cards do not load, it usually means the semantic model connection or refresh needs checking. The design is that these cards are DAX-backed measures from the semantic model."

### Step 3 - Use the Prioritised Review Queue

**What to click**

1. Click the status filter.
1. Select `Any` if the default view hides approved rows.
1. Use search or risk/status filters if useful.
1. Click a transaction.

**What the audience sees**

A ranked finance review queue and a detail drawer.

**What to say**

> "This is where the app becomes different from a report. A report can show me risk. This app gives me a prioritised queue of items to work through."

**Why finance should care**

Finance teams can prioritise limited review time around value, risk, due date, capex, and working-capital exposure.

**Backup line if queue is empty**

> "The default filter may be hiding rows. I will switch to `Any`. If it is still empty, the semantic model likely needs a refresh."

### Step 4 - Click a cost centre

**What to click**

Click a cost centre in the summary section.

**What the audience sees**

The queue focuses on that cost centre.

**What to say**

> "This lets finance move from an overall control view into a cost-centre review. It supports the conversation finance business partners have with budget owners."

**Why finance should care**

Budget conversations need both summary variance and transaction-level evidence.

### Step 5 - Open the review drawer

**What to click**

Click a transaction in the queue.

**What the audience sees**

The transaction review drawer opens with analytical context and workflow fields.

**What to say**

> "The top of the drawer is analytical context. The lower part is app-owned workflow: status, owner, finance note, next action, and escalation."

**Why finance should care**

This is where insight becomes accountable follow-up.

### Step 6 - Save a review action

**What to click**

1. Click assign to self if available.
1. Set a review status.
1. Add a short finance note, for example:

   ```text
   Review with cost centre owner before month-end close.
   ```

1. Set next action if available.
1. Save or mark reviewed.

**What the audience sees**

The app saves the review action.

**What to say**

> "This write-back is not going into the semantic model. It is stored in a Rayfin-managed SQL entity for review actions. That is the clean separation: governed analytics from the semantic model, operational state in the app database."

**Why finance should care**

Finance can keep an audit-friendly decision trail close to the analytical context.

**Backup line if save fails**

> "If save is unavailable, I will not force it live. The intended architecture is that Rayfin stores review state in the `FinanceReviewActions` table. The analytical model remains read-only."

### Step 7 - Explain the "not just a report" moment

**What to click**

Show the architecture/explainer section if visible.

**What the audience sees**

An explanation of the semantic-model and workflow split.

**What to say**

> "This is not a Power BI report embedded in a page. The app is querying the semantic model and then adding application behaviour around it: prioritisation, assignment, notes, status and escalation flags."

**Why finance should care**

This supports controlled finance workflows, not only analysis.

## Explain the difference between the apps

Use this live explanation:

> "The first app shows the direct SQL pattern. The finance data is stored in the Fabric App SQL database, and Rayfin gives the frontend a typed way to read it. That is useful for simple operational apps."

Then say:

> "The second app adds a semantic model. That means finance measures such as spend, variance, overdue amount, and working-capital exposure are governed and reusable. The app consumes those trusted measures, then Rayfin captures the workflow state: who owns the review, what note was added, what status was selected, and whether it was marked for escalation."

Plain-English comparison:

| Finance user question | Best pattern |
| --- | --- |
| "Can I quickly review a small operational SQL dataset?" | App 1 |
| "Can I use governed finance measures and still capture actions?" | App 2 |
| "Do I just need analysis?" | Power BI report |
| "Do I need analysis plus action?" | Fabric App built with Rayfin |

## Close

Use this closing script:

> "To connect this back to the presentation: Rayfin is how we build the app. The Fabric App is what runs inside Microsoft Fabric. The SQL database gives the app a managed operational store. The generated API layer connects the frontend to that data. In the second app, the semantic model connects the same finance data to governed analytics."

Then say:

> "For finance, the value is not that this replaces Power BI. The value is that when a finance process needs interaction, write-back, ownership, notes, and decision support, Fabric Apps give us a way to build that close to Fabric data and governance."

Final line:

> "Reports help us understand what happened. Fabric Apps built with Rayfin help us decide what to do next."

## Demo readiness checklist

Before the session:

- Both apps open from Fabric App URLs.
- You are already signed in.
- App 1 SQL rows are seeded.
- App 2 SQL source rows are loaded.
- App 2 semantic model has been refreshed.
- App 2 review write-back has been tested.
- Status filter in App 2 is understood.
- You have a known transaction to click in each app.
- You avoid showing secret-bearing files.
- You do not overclaim RLS, external notifications, or production finance controls.

