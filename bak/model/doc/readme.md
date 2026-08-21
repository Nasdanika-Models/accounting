An [Ecore](https://medium.com/nasdanika/modeling-democracy-how-ecore-and-visual-first-workflows-put-mbse-back-in-human-hands-12d6bb426bf3)/Xcore metamodel for double-entry accounting: accounts, commodities, transactions, and, distinctively, *assertions* as first-class entries. It is the foundation for model-based, as-code management of personal and household finances, and it is deliberately domain-neutral: the same metamodel can serve multi-party household ledgers, small-association bookkeeping, and distributed accounting between parties.

This is an early publication to stake the ground and collect feedback. The metamodel comes first; tooling follows.

```drawio-resource
../accounting.drawio
```

[TOC levels=6]

## Motivation

For many years I've used multiple tools to manage personal finances and all of them had limitations. Microsoft Money was category-based and too simple. Online tools keep my data in somebody else's cloud, while I want it in files, under version control (Git), on an encrypted drive that I control. GnuCash is the best of the lot functionality-wise (real double-entry, local files), but its ergonomics show their age and an account holds a single commodity, so multi-currency accounts are not really supported.

Beyond tool limitations, two workflow needs are not served well by anything I've used:

* **Staged reconciliation.** I want to download or import transactions but not reconcile or categorize them right away. First pass: look through them for legitimacy and record the account balance. Categorization is a separate, later activity. Most tools conflate import, review, and categorization into a single step.
* **Balance as an assertion.** With some financial institutions a running balance computed from transactions may slip due to an accidental transaction duplication, removal, or edit. Such an edit might even be on another account: a mistake in assigning a correspondent account. Finding the discrepancy can take hours. I want to record a statement balance as a first-class assertion with provenance, and I want verification to tell me *in which interval, on which account* the computed balance diverged from asserted reality.

A typed metamodel makes both of these representable, queryable conditions instead of mental notes and shell scripts.

## Core concepts

* **Ledger**: the root container, one per household or per concern. Contains accounts, commodities, and import sources.
* **Account**: hierarchical. An account may hold balances in multiple commodities (currencies, securities, points, miles). The hierarchy is a containment tree; accounts may also carry cross-references.
* **Commodity**: currencies, securities, and anything countable (airline miles, HSA dollars).
* **Entry**: the key design decision. Entries are of two general types:
    * **Assertion**: a statement of fact about an account at a date, e.g. a balance per statement. Assertions carry provenance: which statement, which import, which manual observation.
    * **Transaction**: a movement, with or without a corresponding transaction on another account.
    * A transaction can *also* be an assertion (a confirmed transfer where both the movement and the resulting balance are known facts); the metamodel supports this via multiple inheritance.
* **Party and roles**: people and institutions related to accounts through typed roles: holder, joint holder, custodian, authorized user, beneficiary, power of attorney, executor, reviewer, auditor, counterparty. Domain terms first, with RACI as a classification layer over them for responsibility reports. Role assignments propagate down the account hierarchy with override. Roles make continuity documentation generable per role (the executor view, the power-of-attorney view) and provide the structure for per-role visibility in shared ledgers.
* **Source**: a record of where entries came from (QFX/OFX file, institution download, manual entry), enabling the staged workflow and audit.
* **Entry lifecycle**: imported, reviewed (legitimacy check), reconciled/categorized. State is explicit in the model.

Verification is a model operation: walk each account's timeline, fold transactions between consecutive assertions, and report exactly which interval on which account fails to reconcile. The "slipped balance" hunt becomes a generated report.

A deliberate exclusion: budgeting and forecasting stay out of this metamodel. Assertions and transactions are facts; budgets are intentions. Intentions belong in a separate model referencing this one, keeping the factual core clean.

## As-code storage and authoring

Because the model is Ecore-based, storage format is a resource concern, not a design constraint:

* YAML/JSON for hand editing and meaningful diffs.
* Excel for bulk entry and for meeting people where they already are: the real incumbent in personal finance is the spreadsheet.
* [Draw.io](https://medium.com/nasdanika/draw-first-execute-later-iterate-forever-d6ac29d7aba5) for authoring the account hierarchy and money-flow topology visually.
* QFX/OFX/CSV as import sources.

Model files live in Git, on an encrypted drive or anywhere else. Git provides the audit trail for free: every change to the ledger is a commit, and model-aware diffing ([EMF Compare + Git URI handler](https://medium.com/nasdanika/harnessing-complex-change-with-emf-compare-git-uri-handler-and-genai-c9ee5c8b53e2)) can render "what changed in my finances this month" as a semantic diff rather than a text diff.

## How this relates to existing tools

The closest relatives are the [plain-text accounting](https://plaintextaccounting.org/) tools: Ledger, hledger, and Beancount with Fava. They share the core convictions (files you own, Git, double-entry, tool-processed) and Beancount's `balance`/`pad` directives independently validate the assertion-first instinct. The intent here is a typed superset, not a rival: an importer that reads existing PTA ledgers is table stakes, and PTA users lose nothing by generating their text format from the model. What a typed model adds over a text grammar:

* One metamodel, many serializations (YAML, Excel, Draw.io, generated text), instead of every tool re-parsing a grammar and reimplementing semantics.
* Assertions as entries with provenance and a diagnostic verification workflow, not just pass/fail checks.
* A standard representation for the imported-but-not-yet-categorized state, instead of DIY import scripts.
* Generation: static HTML sites ("family financial report"), documentation, diagrams, visualizations.

Relative to desktop tools (GnuCash, KMyMoney): Git-friendly files instead of opaque XML/SQLite, multi-commodity accounts, and a staged reconciliation lifecycle. Relative to cloud subscription tools: the data is yours, in files, offline-first; the Mint shutdown demonstrated what renting access to your own finances means. Relative to self-hosted web apps (Firefly III, Actual Budget): no server to operate and no app database; reviewable files are the system of record.

## Visualization

All views are renderings of the same model. Planned directions, sharing a rendering substrate with [model-based telemetry](https://medium.com/nasdanika/model-based-telemetry-as-code-cd1541478be6):

* **Analysis views**: verification reports, semantic diffs, Sankey money-flow diagrams generated from the model.
* **A 2D animated view** in the spirit of [Gource](https://gource.io/): accounts as nodes with area proportional to balance on a stable layout, turnover as directional particles along edges, played over time with a scrubber.
* **A rotatable space-time cube** where the third axis is time: accounts become worldlines whose thickness is balance, transactions are rungs between them. 3D only where rotation reveals meaning.

Such visuals also have a financial-education angle: a thirty-second animation of a synthetic credit card ledger (grace period, interest accrual, the minimum-payment trap) can reach people whom no ledger table ever will. Synthetic ledgers keep such explainers safe to build and share.

## Related Nasdanika capabilities

* [Nasdanika core](https://github.com/Nasdanika/core): capability framework, resource loading (YAML, Excel, Draw.io), generation pipeline.
* [Nasdanika CLI](https://github.com/Nasdanika/cli): command chains for import, verification, report and site generation.
* [Semantic mapping / NSML](https://medium.com/nasdanika/semantic-mapping-3ccbef5d6c70): mapping institution formats and merchant strings to accounts and categories as versioned, shareable artifacts.
* Encryption and signatures: feature-level model encryption so a ledger can live safely in ordinary cloud storage, with visibility policies attached to roles rather than individuals; signatures enable non-repudiable exchange of correspondent entries between parties (the long-range distributed accounting idea).
* [Executable graphs and diagrams](https://medium.com/nasdanika/general-purpose-executable-graphs-and-diagrams-8663deae5248): budgeting and what-if computation over the model, in the separate intentions model.
* AI on typed models: agents grounded in the account graph rather than [text-to-SQL guesswork](https://medium.com/nasdanika/beyond-text-to-sql-why-agents-need-semantic-context-models-to-query-databases-bfaa0464e5e0), proposing typed changes a human confirms. Local files mean AI can be applied selectively and offline-first.

## Status and feedback

The metamodel (`accounting.xcore`) is the current deliverable. If you are a plain-text accounting user, a GnuCash refugee, or someone keeping a household ledger in a spreadsheet, I'd like to hear where this model does not fit your reality: open an issue.

More context: [docs.nasdanika.org](https://docs.nasdanika.org/index.html) and the [Nasdanika Medium publication](https://medium.com/nasdanika/all).

