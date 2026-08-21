An [Ecore](https://ecore.models.nasdanika.org/)/[Xcore](https://wiki.eclipse.org/Xcore) metamodel for double-entry accounting: accounts, commodities, transactions, and, distinctively, *assertions* as first-class entries.
It is the foundation for model-based, as-code management of personal and household finances, and it is deliberately domain-neutral: the same metamodel can serve multi-party household ledgers, small-association bookkeeping, and distributed accounting between parties.

It is also a floor of the [Nasdanika model tower](https://nasdanika.com/models.html), sitting above [lifecycle](https://lifecycle.models.nasdanika.org/) and below the decision, governance, and work floors.
A commodity is anything countable - dollars, hours, tokens, story points - so the floors above use the same double-entry vocabulary for time spent on work, LLM tokens burned by agents, cloud spend per architecture element, and the cost of controls and decisions, all rolling up along containment hierarchies.
If all you need is accounting, the floors above are absent and the floors below are the water heater in the basement;
if you are using the tower, everything in it can carry a ledger.

```drawio-resource
../accounting.drawio
```

[TOC levels=6]

## Motivation

For many years I've used multiple tools to manage personal finances and all of them had limitations.
Microsoft Money was category-based and too simple.
Online tools keep my data in somebody else's cloud, while I want it in files, under version control (Git), on an encrypted drive that I control.
GnuCash is the best of the lot functionality-wise (real double-entry, local files), but its ergonomics show their age and an account holds a single commodity,
so multi-currency accounts are not really supported.

Beyond tool limitations, two workflow needs are not served well by anything I've used:

* **Staged reconciliation.** I want to download or import transactions but not reconcile or categorize them right away. First pass: look through them for legitimacy and record the account balance. Categorization is a separate, later activity. Most tools conflate import, review, and categorization into a single step.
* **Balance as an assertion.** With some financial institutions a running balance computed from transactions may slip due to an accidental transaction duplication, removal, or edit. Such an edit might even be on another account: a mistake in assigning a correspondent account. Finding the discrepancy can take hours. In some institutions transactions get different dates and IDs once a statement is closed - downloading a period of transactions results in duplicated transactions. I want to record a statement balance as a first-class assertion with provenance, and I want verification to tell me *in which interval, on which account* the computed balance diverged from asserted reality.

A typed metamodel makes both of these representable, queryable conditions instead of mental notes and shell scripts.

## Core concepts

* **Ledger**: the root container, one per household or per concern. Contains accounts, commodities, and import sources.
* **Account**: hierarchical. An account may hold balances in multiple commodities (currencies, securities, points, miles). The hierarchy is a containment tree; accounts may also carry cross-references.
* **Commodity**: currencies, securities, and anything countable (airline miles, HSA dollars, plants and hand-woven socks and hats you sell on Etsy).
* **Entry**: the key design decision. Entries are of two general types:
    * **Assertion**: a statement of fact about an account at a date, e.g. a balance per statement. Assertions carry provenance: which statement, which import, which manual observation.
    * **Transaction**: a movement, with or without a corresponding transaction on another account.
    * A transaction can *also* be an assertion (a confirmed transfer where both the movement and the resulting balance are known facts); the metamodel supports this via multiple inheritance.
* **Party and roles**: people and institutions related to accounts through roles: holder, joint holder, custodian, authorized user, beneficiary, power of attorney, executor, reviewer, auditor, counterparty. Role assignments propagate down the account hierarchy with override. Roles make continuity documentation generable per role (the executor view, the power-of-attorney view) and provide the structure for per-role visibility in shared ledgers.
* **Source**: a record of where entries came from (QFX/OFX file, institution download, manual entry), enabling the staged workflow and audit.
* **Entry lifecycle**: imported, reviewed (legitimacy check), reconciled/categorized - a [lifecycle](https://lifecycle.models.nasdanika.org/) catalog with guarded transitions, not an enum. Accounting periods are lifecycles too: period close is a guarded transition, and "no entries into a closed period" is a guard rather than a convention. Segregation of duties (the importer may not reconcile) is an IAM-guarded transition, inherited rather than implemented.
* **Evaluated amounts**: a transaction amount may be supplied by an evaluator - an expression over the model - turning a ledger into a spreadsheet with formulas. That is a double-edged sword: a ledger whose numbers change retroactively is not a ledger. The discipline is *facts freeze, estimates re-evaluate*: evaluation strategy is explicit (evaluate once and freeze; live formula; re-evaluate on demand), and every evaluation is recorded - date, inputs, resulting scalar - so an estimate has an audit trail. Evaluated amounts are what make model templates work: an architecture, a process or org template ships with cost formulas that evaluate against the instantiating model. It is somewhat similar to cloud cost calculator - pick a larger VM - higher cost. Force a JavaScript or Python code on a Java team - higher cost, higher risk (not a commodity) -> higher expected loss if the risk materializes. Add an MCP where a tool call would suffice or a vector database where an in-memory index is more than enough - more reviews - higher time and cost.

Verification is a model operation: walk each account's timeline, fold transactions between consecutive assertions, and report exactly which interval on which account fails to reconcile. The "slipped balance" hunt becomes a generated report.

Budgeting and forecasting may be implemented with special transaction and assertion types/qualifiers - not in the model currently, but can be added
when needed.
Such transactions/assertions might be conditional. For example, only for future dates.

## Position in the tower

Accounting is a spine floor: its extension point, `Accountable`, extends lifecycle's `Staged`, and every floor above extends `Accountable` - so decisions, controls, work items, architecture elements, and agents can all carry accounts and entries, and amounts roll up along the containment hierarchies those floors already define.

What the floors below contribute: IAM gives per-role visibility in shared ledgers (the executor sees one view, the authorized user another); seal gives signed entries - a statement balance with non-repudiable provenance, and signed correspondent entries exchanged between parties; lifecycle gives entry states and accounting periods as data with guarded transitions.

What the floors above do with it, in one commodity vocabulary:

* **Work** records time spent as transactions (with a correspondent leg at the worker), remaining time and effort as assertions, and cost of materials as entries - the worklog/estimate/remaining triple of an issue tracker, generalized to double-entry.
* **Decisions** carry cost/benefit: estimated cost of an alternative as evaluated transactions, actuals as facts, earned value against sunk cost as a fold over entries.
* **Governance** can state what compliance costs: control operation, audits, waivers - and quantified risk (expected loss in a monetary commodity, asserted on assets) becomes comparable with the cost of the controls that reduce it.
* **Architecture** gets FinOps: cloud and run costs attach to the elements that incur them and roll up the containment tree.
* **Capabilities** can declare what it costs to build, to advance a maturity stage, and to run - build-vs-buy with receipts.
* **Agents** get fine-grained cost attribution: tokens burned are transactions referencing the agent, attributable down to the feature ("10K tokens on documentation, 5K on estimation").

[Telemetry](https://telemetry.models.nasdanika.org) is accounting: an OpenTelemetry monotonic counter is a stream of transactions, a gauge is an assertion, and [model-based telemetry](https://medium.com/nasdanika/model-based-telemetry-as-code-cd1541478be6) aligns metrics to spans and therefore transactions to units of work.
Software metrics fit the same shape - a commit is a transaction with additions, deletions, and files-changed commodities; coverage and lines of code are assertions - one mental model and one UI for ledgers, telemetry dashboards, and engineering metrics alike.

Accounts positioned in a model also acquire *graph coordinates*, which is what serious forecasting wants: hierarchical time-series forecasting reconciles predictions across an aggregation structure, and the containment hierarchy is that structure, declared rather than configured.
Clustering by graph neighborhood gives pooled models and cold-start estimates for new accounts.
The forecasting itself, being intention rather than fact, lives in companion models - this floor supplies the coordinates.

## As-code storage and authoring

Because the model is Ecore-based, storage format is a resource concern, not a design constraint:

* YAML/JSON for hand editing and meaningful diffs.
* Excel for bulk entry and for meeting people where they already are: the real incumbent in personal finance is the spreadsheet.
* [Draw.io](https://medium.com/nasdanika/draw-first-execute-later-iterate-forever-d6ac29d7aba5) for authoring the account hierarchy and money-flow topology visually.
* QFX/OFX/CSV as import sources.

Model files live in Git, on an encrypted drive or anywhere else.
Git provides the audit trail for free: every change to the ledger is a commit, and model-aware diffing ([EMF Compare + Git URI handler](https://medium.com/nasdanika/harnessing-complex-change-with-emf-compare-git-uri-handler-and-genai-c9ee5c8b53e2)) can render "what changed in my finances this month" as a semantic diff rather than a text diff.

## How this relates to existing tools

The closest relatives are the [plain-text accounting](https://plaintextaccounting.org/) tools: Ledger, hledger, and Beancount with Fava.
They share the core convictions (files you own, Git, double-entry, tool-processed) and Beancount's `balance`/`pad` directives independently validate the assertion-first instinct.
The intent here is a typed superset, not a rival: an importer that reads existing PTA ledgers is table stakes, and PTA users lose nothing by generating their text format from the model.
What a typed model adds over a text grammar:

* One metamodel, many serializations (YAML, Excel, Draw.io, generated text), instead of every tool re-parsing a grammar and reimplementing semantics.
* Assertions as entries with provenance and a diagnostic verification workflow, not just pass/fail checks.
* A standard representation for the imported-but-not-yet-categorized state, instead of DIY import scripts.
* Generation: static HTML sites ("family financial report"), documentation, diagrams, visualizations.

Relative to desktop tools (GnuCash, KMyMoney): Git-friendly files instead of opaque XML/SQLite, multi-commodity accounts, and a staged reconciliation lifecycle.
Relative to cloud subscription tools: the data is yours, in files, offline-first; the Mint shutdown demonstrated what renting access to your own finances means.
Relative to self-hosted web apps (Firefly III, Actual Budget): no server to operate and no app database; reviewable files are the system of record.

## Visualization

All views are renderings of the same model.
Planned directions, sharing a rendering substrate with [model-based telemetry](https://medium.com/nasdanika/model-based-telemetry-as-code-cd1541478be6):

* **Analysis views**: verification reports, semantic diffs, Sankey money-flow diagrams generated from the model.
* **A 2D animated view** in the spirit of [Gource](https://gource.io/): accounts as nodes with area proportional to balance on a stable layout, turnover as directional particles along edges, played over time with a scrubber.
* **A rotatable space-time cube** where the third axis is time: accounts become worldlines whose thickness is balance, transactions are rungs between them. 3D only where rotation reveals meaning.

Such visuals also have a financial-education angle: a thirty-second animation of a synthetic credit card ledger (grace period, interest accrual, the minimum-payment trap) can reach people whom no ledger table ever will.
Synthetic ledgers keep such explainers safe to build and share.
