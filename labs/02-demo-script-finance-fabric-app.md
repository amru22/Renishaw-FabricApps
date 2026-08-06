# Lab 02 - Customer Demo Script for the Renishaw Finance Control Fabric App

## Demo objective

Show Renishaw how Microsoft Fabric Apps can be used to create a governed internal finance application on top of Fabric, without positioning it as a full enterprise application replacement.

The demo should take **20-25 minutes** when delivered slowly.

## Demo setup

Have these tabs open before the call:

1. Fabric workspace containing the deployed app.
1. Deployed Fabric App URL.
1. The app's child SQL database item in Fabric.
1. This markdown file for your speaker notes.
1. Optional: the GitHub repository with the lab files.

Do not build the entire app live in front of the customer. The demo should show the completed app and explain how it was built.

## Opening story - 2 minutes

Say:

> "For this demo, I have built a small finance control app using Fabric Apps. The scenario is a manufacturing finance team that needs to monitor supplier spend, forecast variance, invoice risk, and working-capital impact across cost centres."

Then say:

> "The point is not that this is a finished finance product. The point is to show the pattern: when a process is too important for spreadsheets, but too lightweight for a full custom application project, Fabric Apps gives us a governed way to build and host that process directly in Fabric."

Business context to mention:

- Manufacturing finance teams often track supplier spend, expedited freight, energy volatility, capex approvals, and accruals.
- These processes often start in spreadsheets.
- The risk is that spreadsheets become disconnected from governance, identity, and operational data.
- Fabric Apps gives a route to a controlled internal tool.

## Section 1 - Show the app as a Fabric item - 3 minutes

In the Fabric workspace, show the **Renishaw Finance Control Tower** app item.

Say:

> "The first thing I want to point out is that this is a Fabric item. It lives in the workspace alongside the rest of the analytics estate."

Show:

- The Fabric App item.
- The app URL.
- The child services if visible.
- The SQL database child item.

Explain:

> "Fabric Apps gives me managed services for the app backend, data persistence, authentication, and static hosting. I do not need to separately provision a web server, identity layer, database API, and hosting pipeline just to prove this process out."

Customer-friendly message:

> "For Renishaw, the value is that an operational data app can sit close to Fabric data and Fabric governance."

## Section 2 - Open the deployed finance app - 3 minutes

Open the deployed app URL.

Start on the **Finance overview** page.

Show:

- Total spend.
- Forecast recoveries or savings.
- Net forecast impact.
- High-risk item count.
- Items in review.

Say:

> "This page gives the finance user the first answer they usually need: where are we exposed, what is driving the variance, and what needs attention?"

Point out that the app uses synthetic finance data:

> "The data here is synthetic, but I have shaped it around realistic manufacturing finance topics: tooling, energy, freight, capex, IT renewals, contractor cover, and service recovery."

Avoid overclaiming:

> "This is not replacing an ERP system. It is a controlled application layer for a targeted process or workflow."

## Section 3 - Demo the cost centre view - 4 minutes

Navigate to the **Cost centre** view.

Show these cost centres:

- Precision Machining.
- Additive Manufacturing.
- Global Supply Chain.
- Facilities and Energy.

Say:

> "Here I can move from overall finance exposure into the operating structure of the business. Each cost centre has an owner, a monthly budget, a quarterly budget, and a working-capital priority."

Click or highlight **Global Supply Chain**.

Say:

> "Global Supply Chain is useful for this story because the data includes expedited freight. That is a classic manufacturing finance issue: we can protect customer delivery dates, but we create premium logistics cost and working-capital pressure."

Click or highlight **Facilities and Energy**.

Say:

> "Facilities and Energy gives us another realistic driver: energy volatility. The important thing is that the app separates controllable operational spend from external volatility, so the finance conversation becomes more precise."

What to explain technically:

> "Behind this page, the app is querying the generated GraphQL API from the Fabric Apps backend. The developer works with typed data models, while the business user gets a simple app experience."

## Section 4 - Demo the risk queue - 5 minutes

Navigate to the **Risk queue**.

Filter or sort to high-risk items.

Open these examples:

| Transaction | Story |
| --- | --- |
| `FIN-0013` | Expedited freight in Global Supply Chain. |
| `FIN-0021` | Energy price volatility in Facilities and Energy. |
| `FIN-0007` | Capex approval threshold in Additive Manufacturing. |
| `FIN-0011` | Contractor cover for Metrology Services backlog. |

Say:

> "This is the most important page in the demo. It turns finance data into a work queue. Instead of asking someone to scan a spreadsheet, the app shows which items are high risk, in review, or above a tolerance threshold."

For `FIN-0013`, say:

> "This item is not just an amount. It has a business explanation: air freight was used to protect customer delivery dates. That is exactly the kind of context a finance business partner needs before challenging or approving the spend."

For `FIN-0021`, say:

> "Energy is marked high risk because it is above plan and can materially distort the monthly view. This is where finance can separate market-driven variance from controllable operational actions."

For `FIN-0007`, say:

> "The capex example shows that the same app can handle approval-style review, not just passive reporting."

Key message:

> "Fabric Apps is useful when the insight needs to become an action or a workflow."

## Section 5 - Show transaction detail - 3 minutes

Open a transaction detail page.

Show:

- Supplier.
- Category.
- Amount.
- Due date.
- Status.
- Risk level.
- Variance driver.
- Finance owner.
- Commentary.

Say:

> "A report is good at showing patterns. An app is good at supporting decisions. This detail view gives the person reviewing the item the operational context needed to decide what to do next."

Explain the app pattern:

> "The finance user does not need to understand the backend. The developer has defined the transaction model once, and Fabric Apps provides the API and data layer behind the screen."

## Section 6 - Show how it was built - 4 minutes

Switch back to the Fabric workspace and, optionally, the code repository.

Show:

- The app item in Fabric.
- The child SQL database.
- The local project structure or GitHub repository.
- The lab build file.

Say:

> "The build pattern is straightforward: define the data models, build the frontend experience, test locally, and deploy with the Rayfin CLI."

Show or describe the two model concepts:

- `FinanceTransaction`.
- `BudgetTarget`.

Say:

> "These models are the important part. They describe the business process. Fabric Apps then generates the database schema and GraphQL API from that model."

Then say:

> "This is why Fabric Apps is interesting for internal apps. We are not starting with infrastructure. We are starting with the business process."

## Section 7 - Close with value and positioning - 2 minutes

Use this closing:

> "The takeaway is that Fabric Apps gives us a new option inside Fabric. We can build governed, authenticated, data-driven apps for focused internal processes. For Renishaw, that could mean finance controls, engineering exceptions, supplier risk queues, service operations, or manufacturing quality review."

Then position the boundaries:

> "I would not position this as a replacement for ERP, PLM, or a large enterprise application. I would position it as a fast way to create controlled operational experiences around Fabric data where a spreadsheet or static report is no longer enough."

## Suggested questions to ask Renishaw

Use these if you want to make the demo interactive:

1. "Where do you currently have spreadsheet-based finance or operational review processes that need more control?"
1. "Which workflows need both data visibility and a decision or action?"
1. "Are there teams that need a simple internal app but do not have budget or time for a full application build?"
1. "Which Fabric datasets or lakehouse tables would be useful to expose through a targeted app experience?"

## Things to avoid saying

Avoid:

- "This replaces your ERP."
- "Business users can build every app without developer involvement."
- "This removes the need for governance."
- "This is a production-ready finance product."

Instead say:

> "This is a governed app pattern for focused internal processes."

## Backup demo flow if time is short

If you only have 10-12 minutes:

1. Show the Fabric App item.
1. Open the deployed app.
1. Show the finance overview.
1. Open the risk queue.
1. Drill into `FIN-0013`.
1. Explain the model-first build pattern.
1. Close with the value statement.

## Final one-minute summary

Say:

> "In 20 minutes, we have shown a finance control app running as a Fabric item, using Fabric authentication, a generated data API, a managed SQL database, and static hosting. The main value is speed with governance: a targeted internal app close to the Fabric data platform."

