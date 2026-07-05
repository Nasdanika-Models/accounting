
A pragmatic, just-enough Xcore metamodel for product management as code. Elements are federated text-based artifacts authored in version control, published as Maven artifacts, and consumed across teams.

```drawio-resource
../product-management.drawio
```

[TOC levels=6]

## Executive summary

Most product strategy work goes into *push*: positioning statements, feature briefs, vendor pitches, competitive landscape decks, talking points prepared for the next steering committee. Every capability owner pushes a narrative; every consumer absorbs whichever narrative reached them first or was packaged most insistently. Decisions get made on rhetoric, and the rhetoric is optimized for the room rather than for the consumer.

The product management model flips the direction. Personas - the consumers - articulate concerns: goals to reach, needs to satisfy, pain points to resolve. Capabilities are *pulled* by those concerns. The question "what addresses this concern of this persona, with what evidence, under whose authority, at what maturity?" has a queryable answer over the federated graph. Positioning, executive summaries, and adoption decisions become derived views of the model rather than separately authored artifacts that drift out of sync with what is actually built.

Engineers will recognize the shift. It is the same shift as **reactive streams with back-pressure** (consumers signal demand, producers respond) over synchronous push, and as **backward chaining** (start from the goal, trace back to whichever facts and rules satisfy it) over forward-chaining production systems. The [Nasdanika Capability framework](https://docs.nasdanika.org/capability/) operates on the same principle: capability requests are satisfied by whichever providers can serve them, discovered at the point of need rather than wired in advance.

Four consequences follow that are hard to obtain in the push mode:

- **Decisions cite the model rather than the loudest voice.** Evaluating a proposal becomes a query over personas, concerns, evidence, dependencies, and authority. The arena moves from rhetoric to structure.
- **Internal and external offerings are evaluated on equal terms.** A vendor product, an internal platform, and an early-stage internal proposal enter the model in the same shape. The political asymmetry that otherwise favors the vendor narrative is removed from the input stage.
- **Communication is calibrated to the audience by construction.** A senior leader, an architect, and a delivery lead each see the view of the same capability that fits their level of abstraction, derived from one model. The simplification is no longer lossy; the next layer of detail is one link away.
- **AI assistance becomes useful where it previously stalled.** An agent reasoning over a typed federated graph can answer pull queries with citations, route follow-ups deterministically, and operate as a first-class consumer alongside humans.

The remainder of this page describes the metamodel, the use cases that motivated its design, and the execution layer that will carry persona-pull queries from articulation to assembled response.

## Intent: elicitation, reification, prioritization

Strip the metamodel to its spine and it is a pipeline that carries intent from whoever holds it to whatever satisfies it:

> **persona → concern → capability → capability provider**

Each arrow is a distinct act on intent, and the model gives each one a typed, queryable home:

- **Elicitation.** A *persona* is whoever holds the intent; a *concern* - a goal to reach, a need to satisfy, a pain point to resolve - is that intent made explicit. The model does not conduct the interview, but it shapes it: by defining what is worth capturing it forces the questions *whose concern? how do we know it is real? how urgent?* to be answered rather than assumed.
- **Reification.** Personas, concerns, capabilities, and providers are typed elements in a federated graph, not prose in a deck. Intent stops being a sentence in a document and becomes an addressable artifact with identity, provenance, and validity - something a teammate, a tool, or an agent can cite and act on.
- **Prioritization.** Because capabilities are *pulled* by concerns and carry evidence, dependencies, authority, and maturity, "what should we do next?" is a query over structure rather than a contest of narratives. Frameworks like RICE, WSJF, and AHP layer on top (see *Capability prioritization driven by evidence rather than volume*) rather than replacing the graph.

This framing is becoming the point rather than a nicety. When coding agents make implementation cheap, the scarce and decisive artifact is no longer the code - it is a precise, shared, machine-readable statement of intent: whose concern is served, on what evidence, and whether it matters more than the next thing. The bottleneck moves upstream, from *building the thing* to *knowing which thing is worth building, and why*. The model is exactly that upstream artifact, and because it is typed and federated it serves an agent on both ends: as **input** it states what to build and the concern it must satisfy, closing the gap that otherwise makes "just write the code" produce confident, wrong output; as a **reasoning substrate** it lets the agent ask whether the thing is worth building at all, and answer with citations. The economics of cheap, speculative capture are developed further in the *Synergy discovery* and *Synergy versus competition* sections below.

## Get started

Four ways in, from least to most committed.

### Watch the brief - 1.5 minutes

<div style="max-width: 720px" class="embed-responsive embed-responsive-16by9">
    <iframe src="https://www.youtube.com/embed/h8vgsqn2qHI?si=n2RR5kyA1pT-2voj" title="Product Management As Code" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
</div>

A brief overview of what the model addresses and what it gives you. 

### Try the model on your own problem - about 10 to 15 minutes

The model is useful as a reasoning substrate even before you author your own consuming model. Copy the prompt below into your preferred GenAI assistant (Claude, ChatGPT, Gemini), replace the placeholders with your context, and use the response to organize your thinking. The structured response - phrased in personas, concerns, capabilities, evidence - is itself the first deliverable; you can take it to your team as a starting point for a conversation.

> I work in a development organization with **\<size\>** people. We are adopting GenAI for developer productivity and other use cases. We use multiple technologies - Java on the backend, JavaScript / React on the frontend, Python for AI workloads. Our knowledge artifacts live in Excel, PowerPoint, Confluence, and SharePoint.
>
> We are facing the following problems:
>
> 1. Many tools, agent skills, and other productivity solutions, with developers having trouble discovering them.
> 2. Duplication of effort and overlap between solutions, surfaced politically rather than analytically.
>
> I ran across the Nasdanika Product Management Model (https://product-management.models.nasdanika.org/) and I am wondering how it might help our organization address these concerns. I am also wondering what alternative solutions exist (internal developer platforms, enterprise architecture tools, idea-management systems) and how the Nasdanika model compares on functionality, adoption cost, and ROI. The model is free and open source, but adoption itself is an investment.
>
> Please structure your response in the model's vocabulary - personas, concerns, capabilities, evidence - and surface the gaps in my framing as questions the model would ask. Compare with the alternatives on the dimensions I named. Be honest about where the model is a fit and where it is not.

### Watch the explainer - 8 minutes

<div style="max-width: 720px" class="embed-responsive embed-responsive-16by9">
    <iframe src="https://www.youtube.com/embed/FaWhirsxFmU?si=RNOUzbvSB9_vJ68Z" title="Nasdanika Product Management" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
</div>

A longer audio explainer walking through the model's design, the use cases it addresses, and the agentic execution roadmap.
Watch this when you want depth before committing to the template repository.

### Use the template repository - about 30 minutes to first published page

The [template repository](https://github.com/Nasdanika-Templates/product-management) is a starter you can fork and run.
Click *"Use this template"* on GitHub, enable Pages, edit YAML, push - a GitHub Action publishes your rendered site.
The [example output](https://nasdanika-templates.github.io/product-management/) is what you should expect to see, generated from the template's own sources.

The template is the path from reading about the model to authoring your own consuming model. 

## What this is, in its own terms

This metamodel is itself a product. The page you are reading is the first slice of its own product management model - applied recursively.

### Personas

- **Senior leader driving innovation and AI adoption.** Accountable for portfolio-level outcomes: reducing duplication, increasing adoption of shared platforms, and turning AI investment into measurable productivity. Sees the developer-productivity space where hundreds of developers have built thousands of overlapping tools and is trying to distinguish genuine coverage from noise.
- **Internal innovator.** An individual contributor or small team driving a new idea inside a larger organization. Has no formal mandate to define personas or concerns, and no authority to bind anyone else's roadmap. Authors bespoke persona and concern definitions to frame the problem they're working on - definitions that others may not have seen, may not agree with, and may actively dispute.
- **Enterprise architect.** Maintains capability maps and motivation models, and is asked to defend them against organizational drift, vendor slippage, and political revision. Owns alignment between strategy and delivery.
- **Technical product manager.** Owns a product or platform whose consumers are themselves engineering teams. Needs to express persona-concern-capability traceability with the same rigor used for code.
- **Platform engineering lead.** Operates an internal platform whose roadmap depends on, and is depended upon by, many other teams. Needs durable evidence of who promised what to whom and when.
- **AI agent.** A first-class consumer. Reads the model to answer coverage, gap, impact, and counterfactual questions over a formal graph rather than over unstructured prose.

### Concerns these personas raise

#### Goals

- **See actual coverage, not claimed coverage.** Distinguish capabilities that genuinely address a persona's concerns from capabilities whose providers merely assert that they do. The model should make the gap between claim and evidence inspectable.
- **Surface duplication structurally.** When two or three teams independently build capabilities addressing the same concerns of the same personas, the overlap should be visible from the model rather than discovered by accident at a steering committee.
- **Make consolidation defensible.** Decisions to retire, merge, or anoint a single capability among several overlapping ones should be traceable to the concerns, evidence, and authorities involved - not reducible to executive preference.
- **Make alignment auditable.** Every assertion about a persona, a concern, or a capability should be attributable to an authority with a defined scope and validity period.
- **Make every voice heard.** A dissenting team must be able to publish its own model that references the canonical assertions and adds its own. Authority is explicit, not implicit in who speaks loudest.
- **Make dependencies between capabilities first-class.** A capability that depends on another should say so as part of its contract, not bury the dependency in a wiki page.
- **Plan in shapes, not in dates.** A roadmap's structure (an approval gate triggers a 14-day review window; a release starts a 90-day stabilization period) should be expressible before any calendar dates are known, and should remain valid after they slip.
- **Bind plans to reality without rewriting them.** When the date of an approval, release, or commitment finally lands, every temporal that referenced it should resolve automatically. The plan is the same plan; only the calendar projection changes.
- **Publish a persona without asking permission.** An innovator must be able to define a persona, articulate its concerns, and put the definition on the record without first negotiating it through the organizational hierarchy. The definition exists; whether it is authoritative is a separate question answered by whoever chooses to reference it.
- **Be cited, agreed with, or disputed on the merits.** A bespoke persona should be discoverable and addressable so that others can extend it, reference it, or publish a competing definition. Silence and selective forgetting are not acceptable responses.

#### Needs

- **Portfolio-level reasoning over a federated graph.** Reasoning about duplication and adoption requires a view across many teams' models without forcing all teams onto a single central tool. The federation has to compose into a coherent portfolio view on demand.
- **Adoption visible as a first-class signal.** Whether a capability is actually used by the personas it claims to address is itself evidence, and should attach to the capability rather than live in a separate analytics tool nobody opens.
- **Coexist with disagreement.** Multiple incompatible definitions of "the junior developer persona" should be able to exist simultaneously, each attributable to its author, without one being administratively erased to make the catalog "consistent."
- **Survive personnel and tooling change.** Models authored today must remain readable and verifiable years later, by humans and by machines, without a vendor account
- **Compose across organizational boundaries.** A model from one team must be referenceable from another team's model with the same mechanics used for code dependencies.
- **Reusable templates.** A product launch template, a regulatory approval template, a vendor onboarding template should be authored once with relative temporals and instantiated repeatedly against different concrete anchor events.
- **Honest schedule arithmetic across federation.** When one team's commitment depends on another team's release, the dependent's temporal should be expressed relative to the dependency, not duplicated and silently drift out of sync.

#### Pain points

- **Internal-tool sprawl - hundreds of developers, thousands of tools.** In the developer-productivity space, parallel construction of overlapping internal tools produces more confusion than productivity. Each tool has its own champion, its own asserted persona, and its own claimed coverage; without a shared model, deduplication is impossible and consolidation is a political fight.
- **AI adoption stalls on missing structure.** AI assistance over product strategy works only as well as the structure it has to reason over. Free-form roadmaps, slide decks, and wikis do not give an agent enough to detect duplication, gaps, or drift. The absence of a formal model is itself the bottleneck.
- **Asymmetric blame.** When a high-status promise slips, downstream teams absorb the cost. Without a durable, dated record of who promised what, the political gradient runs only one way
- **The super-chicken problem.** A loud stakeholder claims "nobody needs that" and the concern disappears from the official narrative. Without per-assertion authorship, the claim is indistinguishable from consensus
- **Strategy drift.** Roadmap tools optimize for what was decided last week. The longer arc - which goals were committed to, which were silently abandoned - is lost
- **Date theater.** Calendar dates put into roadmap tools before the underlying events have happened create false precision. They are negotiated, revised, and quietly forgotten; the original intent ("two weeks after approval, whenever that is") is lost.
- **Cascading reschedule churn.** When a single upstream event slips, every downstream date in every dependent plan has to be edited by hand. The edits themselves become a source of error and political revision.
- **Templates that aren't.** Templates expressed in absolute dates aren't templates; they're examples that get copy-pasted and diverge. The relative structure that made them reusable is lost on first use.

### Capabilities this metamodel provides

- **Persona, Concern, Capability as first-class elements.** With explicit relationships: concerns address goals, needs, and pain points; capabilities address concerns; providers supply and require capabilities
- **Temporal validity.** Every named element is a [`Period`](references/eClassifiers/Period/index.html). Assertions, commitments, and provider relationships are valid for stated intervals.
- **Provenance and authority.** Every element carries identity and references to the authority that asserted it. Models published as signed Maven artifacts inherit the provenance of their publication.
- **Federation by construction.** Domains (`PersonaDomain`, `CapabilityDomain`, `CapabilityProviderDomain`, `ActorDomain`) and reference types (`PersonaReference`, `CapabilityReference`, `CapabilityProviderReference`) allow one model to cite another without copying it.
- **Capability dependency and composition.** Capabilities decompose into sub-capabilities and declare prerequisite, enhancing, or conflicting relationships at the contract level, distinct from implementation requirements on a specific provider.
- **Evidence.** Provided capabilities reference evidence, so a claim is grounded rather than asserted. For example, a demo or a case study for a software product. A certificate for a person skill..
- **Roles and assignments - generalized RACI.** Authority over a persona, concern, or capability is itself modeled, with periods and assigned actors. Roles are not fixed to a predefined set such as Responsible, Accountable, Consulted, Informed; a model defines whatever roles its domain needs (Owner, Reviewer, Approver, Sponsor, Steward, Auditor, Dissenter) and assigns actors to them for stated intervals. RACI and its variants are expressible as conventions within this mechanism rather than baked into it.
- **Relative temporals.** A [`Temporal`](references/eClassifiers/Temporal/index.html)  may be anchored to another Temporal (its base) and offset by a Duration, rather than carrying an absolute Instant. "Fourteen days after the security review approval" is a first-class expression, not a comment.
- **Bounded relative temporals.** A Temporal may declare lowerBounds and upperBounds against other temporals, expressing constraints like "no earlier than release, no later than end of fiscal year" without committing to dates.
- **Resolution against concrete events.** When a referenced anchor temporal acquires a concrete Instant - an approval is granted, a release ships - every relative temporal that depends on it resolves to a concrete Instant by composition. Resolution is a derivation, not an edit.
- **Template models with deferred binding.** A model authored entirely in relative temporals is a template. Instantiation against a concrete anchoring context produces a bound model without modifying the template itself, so the template can be reused across products, releases, and engagements.
- **Cross-model temporal references.** A relative temporal can reference an anchor defined in another federated model. A consuming team's commitments can be expressed relative to a platform team's published release temporal, and resolve when the platform team publishes the date.
- **Auditable resolution provenance.** The resolved Instant of a relative temporal carries the chain of anchors and offsets that produced it. When a date changes, the cause is structurally visible: which upstream event moved, by how much, and when.
- **Periods as relative ranges.** A Period whose start and end are relative temporals describes a validity interval whose calendar projection is determined by the events its endpoints reference - supporting roles, capability provision, and assignments that activate automatically when the anchoring events occur..

### Capability providers

- **Nasdanika.** Publishes this metamodel as Maven artifacts. Provides the CLI tooling, generated Web UI, and Xcore sources
- **Git hosting (any).** Provides version control, signed commits, and review. The metamodel does not depend on any particular forge.
- **Maven repositories (any).** Provide artifact distribution and consumer-side dependency resolution. The metamodel does not depend on Maven Central specifically.
- **AI assistants.** Provide reasoning, validation, gap detection, and authoring assistance over the formal graph.

## How to read this site

This site is generated from the metamodel itself.

- **Interactive diagram on this page** - core concepts, click on diagram elements to navigate to context diagrams.
- **Classifiers** - every class, interface, and type in the metamodel, with documentation, attributes, references, and inheritance.
- **Diagram** - the static UML rendering of the package.
- **Graphs** - interactive 2D and 3D views, including renderings that pull in dependencies and subpackages so the federation across reference models is visible.
- **Search** - full-text search across the generated site.

Sources live on [GitHub](https://github.com/Nasdanika-Models/product-management). Releases are published as Maven artifacts.

## Use cases

The metamodel is general; the situations where it pays off are specific. The scenarios below are the ones that motivated its design.

### Documenting a product or platform with persona-concern-capability traceability

A team owning a product or internal platform models its consumers as personas, articulates the goals, needs, and pain points of those personas as concerns, and declares the capabilities it provides as addressing specific concerns. The model lives in version control, is published to Maven, and is rendered as a generated documentation site.

The payoff is not the site. It is that every claim the product makes about its consumers is attributable, every capability is traceable to the concerns it addresses, and adoption decisions by other teams can be made against a model rather than against a sales pitch.

### A home for fine-grained capability documentation

Documentation has a placement problem that grows with the system it describes. A new fine-grained capability - say, a Groovy fenced block for configuring models loaded from Markdown - has to be given a *home*: a subsection on the Markdown model's page, a standalone wiki article, a heading in a reference manual. The choice is made once, under time pressure, by whoever wrote the feature, and it is rarely revisited. The capability is real and the documentation exists, but the binding between them is a location someone picked rather than a relationship the system records. Months later the page has drifted, the capability has grown sub-features that found homes of their own, and no query answers "what documents this?"

At enterprise scale this is its own category of problem. Thousands of knowledge-base articles and Confluence pages describe capabilities that are never named as such. The article describes a thing; the thing has no identity the article can point at; the article is reachable only by search and by whoever remembers it exists. The knowledge base is a flat sea of pages whose relationship to what the organization actually does - and to each other - lives in nobody's model.

The product management model gives every capability, however fine-grained, a first-class element with a stable URI identity. Documentation attaches to that element: inline through `documentation`, or by reference through `docRef` pointing at the Markdown file, wiki page, or knowledge-base article that already exists. The Groovy-fenced-block capability becomes a sub-capability of the Markdown-model capability - composition the model records, not a heading someone chose - and its documentation hangs off it natively. The page no longer has to *be* the home; it is *referenced by* the home. Because identity is by URI and rendering is by path, the same capability can be cited from many places and rendered wherever a consumer's view places it, without the documentation moving or the link breaking.

The payoff is that documentation stops being orphaned from what it describes. "What documents this capability?" and "what capability does this article describe?" become queries over the model rather than acts of recall. A holistic knowledge base emerges as a property of the capability graph - every page anchored to a capability, every capability anchored to the concerns and personas it serves - rather than as a navigation hierarchy someone curates and everyone eventually distrusts. Existing articles are absorbed by reference rather than rewritten: the model supplies the missing layer of identity and relationship that turns a pile of pages into a structured, navigable, queryable knowledge base.

### Capability prioritization driven by evidence rather than volume

A product manager prioritizing capabilities for the next period reasons over the model directly: which concerns of which personas are unaddressed; which concerns are supported by evidence and which are not; which proposed capabilities have prerequisites that are already available and which do not.
Prioritization frameworks - RICE, WSJF, opportunity scoring, AHP, and others - are layered as evaluations on top of the structured graph rather than replacing it. 
The graph supplies the criteria hierarchy that frameworks like AHP require: personas as top-level criteria, each persona's concerns as sub-criteria, capabilities as alternatives. 
Pairwise comparison matrices, where used, attach to the container that owns the elements being compared - the product or persona that owns the concerns - matching the way authority is already expressed elsewhere in the model. 
The `mcda` reference model provides the extension; other methodologies can be modeled the same way without changing the core.

The payoff is that the prioritization is defensible. 
The trail from "we built X" back to "because persona Y had concern Z, supported by evidence W, asserted by authority V, weighted by judgment U" is queryable. 
Consistency of the underlying judgments is itself a checkable property of the model; conflicting judgments from different authorities are visible rather than averaged away.

### Educating consumers about a complex offering

A sophisticated platform with many capabilities is hard to explain in linear documentation.
Consumers don't know what to read because they don't know what they need. 
A capability assembly published to Maven becomes the substrate for a chat interface: consumers ask how to solve their problem, and the chat surfaces the relevant capabilities with provenance and version coordinates.

The payoff is the inversion: rather than explaining capabilities to consumers who may not need them, consumers ask about their problems and discover the capabilities that address them. 
The chat is grounded in the formal model, not in the open internet, so its answers are specific, citable, and verifiable.

### Federated authoring across teams and organizations

Multiple teams author their own models, publish them as Maven artifacts, and reference each other's published models the same way code dependencies are referenced. 
Domains merge by shared identity; anchors allow contributors to attach into named slots within a domain they don't own; references cite individual elements across artifacts.

The payoff is that no central tool, no central admin, and no central database is required for many teams to maintain a coherent shared view of personas, concerns, and capabilities. 
The federation is in the artifacts themselves. 
A consumer's view is whichever set of models they choose to import.

### Persons and teams as capability providers

A resume is, structurally, a list of capabilities with evidence: claims about what the person can do, grounded in projects, certifications, prior employment, and references. The format has converged on prose because resumes are read by humans who tolerate informality, but the underlying information has the same shape as a product management capability set. The same is true of team-level skill inventories, consulting practice descriptions, and contractor offerings - each is a "we can do X, here is the evidence" claim asserted with authority and validity.

The model represents that information natively. A person or team is a capability provider; the capabilities they provide are their skills, technologies, methodologies, and prior outcomes; each carries evidence (projects, certifications, references) with its own provenance. The result is a resume a machine can reason over the same way it reasons over any other capability provider - and that can be referenced from the rest of the federation through the same mechanics any other citation uses.

Two consequences follow that the prose-resume format cannot deliver. The first is that *implementation requirements travel with the capability*. A capability to author Xcore models requires the Eclipse IDE; a capability to operate a specific legacy system requires access to that system; a consulting practice's capability to deliver a particular methodology may require an on-site engagement model. The model carries these prerequisites as first-class structure, so a staffing decision is not made on the assumption that "knowing X" is sufficient when "knowing X with the tooling X requires" is what the work actually needs. Traditional resumes silently lose the tooling and context dependencies; the capability model surfaces them.

The second is *federated discovery of niche expertise*. In any sufficiently large organization, the people who know the legacy systems, the unusual programming languages, the deprecated tools that still run production workloads are tribal knowledge - findable only by asking around. If their capabilities are published in the same federation that describes the organization's products, the niche expertise is discoverable by query rather than by social network. A team about to retire a system nobody has touched in a decade can ask the model for capability providers who have touched it; the answer is structural rather than political.

The payoff is that the resume-and-staffing world and the product-management world stop being separate domains the organization maintains in incompatible formats. The same vocabulary that describes a software product describes the people who can build, operate, and replace it. Capability providers - organizations, teams, individuals - are uniform on the graph; matching needs to capabilities works the same way whether the capability comes from a vendor product or from a person.

### Onboarding contributors: concepts first, details on demand

A contributor joins a team that owns a complex capability - a platform with thousands of classes and hundreds of annotations, its own deployment and operational stack, its own accumulated conventions.
The traditional onboarding path asks them to memorize that surface: the framework's idioms, the command incantations, the catalog of options. 
Most of that surface is now reproducible on demand by an AI assistant. What an assistant cannot supply is why the capability exists, whose concerns it addresses, what it requires and what depends on it.
The model carries exactly that. A contributor reads the capability, follows it to the concerns it addresses, to the personas that raise those concerns, to the providers it depends on and the evidence behind its claims, and reconstructs intent from the graph rather than from whoever happens to be free to explain it.

This inverts the order in which a contributor learns. The prose-and-tribal-knowledge path teaches form first - syntax, annotations, commands - and intent last, if ever.
The model lets a contributor learn intent first - the concerns, the dependencies, the authorities, the lifecycle state - and pick up form on demand, because form is the part an assistant supplies well and the model is the part that tells the assistant which form is wanted.
A contributor who grasps a capability at the level of its concerns can ask precise questions of an assistant and, more to the point, judge whether the answers are right.
A contributor who has only memorized details can do neither. Detail without the surrounding concepts is pedantry rather than mastery, and it is precisely the layer a machine now reproduces for free;
the durable skill is the conceptual grasp that lets a person direct the machine and verify what it returns. Knowing a capability well enough to navigate it and ask the right question is worth more than memorizing the API it is built on,
because the model and the assistant together regenerate the API on demand while neither regenerates understanding of intent.

The payoff is that the model doubles as an onboarding substrate. The same artifact a team maintains for delivery and audit is the one a new contributor reads to become productive, so there is no separate onboarding document to write and let rot,
and no dependency on a senior's availability to transfer the "why" by conversation. A contributor embeds into a capability provider and learns its vocabulary and its surroundings from the same graph everyone else works against.
Mastery becomes learnable from the structure; pedantry stands exposed as the shallow thing it is; and the scarce skill the team actually needs - someone who understands a capability well enough to direct a machine and check its output against the concerns it is supposed to serve - is the skill the model is shaped to teach.

### Accountability for promises and dependencies

A platform team commits to providing a capability by a target time. 
A dependent team plans its own commitments relative to that capability, using temporal references that resolve when the upstream commitment lands. When the upstream commitment slips, the resolved temporals of all dependents change automatically - and the chain of cause is structurally visible in the model.

The payoff is that the asymmetric-blame pattern is interrupted. 
The original promise is a signed, dated, attributable assertion. 
Slippage is not deniable, dependents are not silently blamed for upstream movement, and audit is a query rather than a forensics exercise.

### Surfacing duplication across a portfolio

A senior leader responsible for portfolio outcomes loads the assemblies of many teams together.
Capabilities addressing the same concerns of the same personas - whether or not they share a name - appear adjacent in the merged graph. 
Lifecycle assertions show which are Available, which are In Development, which are Proposed, and whose authority asserts what.

The payoff is that duplication is visible structurally rather than discovered politically.
Consolidation decisions can reference the model: which capabilities have evidence, which have providers, which address concerns no other capability addresses. The model does not make the decision; it makes the decision defensible.

### Graph-driven portfolio visualization and build-order navigation

A portfolio of any size accumulates more candidate capabilities than any reviewer can hold in their head at once. Spreadsheets and roadmap tools collapse the richness into linear lists ordered on one or two visible columns; the relationships that actually drive prioritization - which capabilities serve which personas, which depend on which, what is already built versus still to be built, what each capability would cost to bring online - survive only in the heads of the people who wrote the rows.

The federated model carries those relationships explicitly. A graph view materializes them as a navigable picture rather than a flattened table. In a typical encoding, personas are assigned colors; capabilities take their color from the personas whose concerns they address - blended where they address several - with saturation proportional to the value the capability delivers to those concerns. Node size encodes the effort to build the capability; border thickness encodes the cost of unbuilt dependencies that would need to come first. The view can be filtered to capabilities that are not yet available, surfacing the *frontier* of the portfolio: the terminal nodes where building unlocks the most value for the least cost. The same picture answers questions a spreadsheet cannot, because frontier-of-a-table is not a thing - which unbuilt capability sits closest to delivering the most value to the personas that matter most, and which build sequence reaches it most cheaply.

The graph is generated from the model rather than authored alongside it. Multiple rendering targets - interactive 2D, 3D, draw.io, PlantUML - fall out of a generic diagram intermediate so the visualization is a function of the model rather than of any single tool. A capability page is reachable by drilling into its node; on that page, the value-by-persona breakdown, the dependency tree, the evidence, and the lifecycle history are derived from the same source the graph was. An accompanying table view sits next to the graph for readers who want sortable columns; the two views are reflections of one model, not two artifacts to maintain.

The payoff is that portfolio decisions stop being negotiations over which row to look at first. Structural fit, build cost, and the dependency order that unlocks compound value are visible at once, in the same picture. A reviewer who reads graphs better than tables can read the graph; a senior leader who skims sees the headline at a glance; an engineer who wants the detail follows the link from a node into the underlying capability page. The investment in the visualization pays back across every audience that consumes the model, because the model itself does not have to be re-shaped for any of them.

### Formalized innovation

An internal innovator articulates a persona and a set of concerns no existing capability addresses, proposes a capability, and elaborates it - declaring prerequisites, dependencies, evidence, and the authority asserting each. 
The capability moves through lifecycle states (Proposed → Elaborated → Approved → In Development → Available) as different authorities bind decisions to it.

The payoff is that elaboration is a durable, citable artifact at every stage. 
Withdrawn and Superseded capabilities remain searchable so future innovators can build on prior elaboration rather than restarting.
The innovation program produces artifacts in the same shape as the production capability set, eliminating the handoff loss.

### Coexistence of bespoke and authoritative definitions

An innovator publishes a persona definition for "junior developer" that does not match the platform team's official definition.
Both definitions are first-class. Each is attributable to its author. Consumers choose which to reference.
A senior leader can compare them, surface the disagreement, and decide whether to converge or maintain plural definitions.

The payoff is that disagreement is legible rather than suppressed. The super-chicken pattern - a loud stakeholder declaring "nobody needs that" - does not erase the dissenting definition. 
Every voice has a home; authority is explicit; the catalog reflects what the organization actually believes rather than what its loudest members claim it believes.

### Template models with relative temporals

A product launch playbook, a regulatory approval process, or a vendor onboarding sequence is authored once as a model with relative temporals: dates expressed as offsets from anchor events ("fourteen days after security review approval").
The template is instantiated against a concrete context - a specific product, a specific approval - and resolves to concrete dates as the anchor events occur.

The payoff is that templates are actually reusable.
The structural relationships between events survive across instantiations; only the calendar projection changes.
When anchor events slip, dependent dates resolve to new values without manual editing.

### AI agents reasoning over product strategy

An AI agent is given access to a capability assembly and asked to validate the model, propose missing concerns, identify capabilities that address no concerns, find personas with unaddressed goals, suggest consolidations of duplicated capabilities, or generate starter solutions composing multiple capabilities. 
The reasoning is over a formal graph with explicit relationships, not over unstructured prose.

The payoff is that AI assistance is grounded. Answers cite specific capabilities at specific versions. 
Generated artifacts respect declared prerequisites and conflicts. 
The same substrate that supports human reasoning supports machine reasoning, and the citations are the same.

### Governing a catalog of tools an agent can call

An agent platform accumulates callable tools the way a portfolio accumulates capabilities, and it accumulates them faster. Model Context Protocol (MCP) servers publish their tools into a flat namespace, and the catalog grows by aggregation rather than design: a team connects a new server, a vendor ships an update, and the agent's tool list silently lengthens. The failure mode is the one any over-large catalog produces. Function-calling accuracy degrades as the list grows and as semantically similar tools crowd each other, with reported accuracy collapsing toward random selection once a catalog passes roughly a hundred tools ([RAG-MCP and related work](https://www.microsoft.com/en-us/research/blog/tool-space-interference-in-the-mcp-era-designing-for-agent-compatibility-at-scale/)). The descriptions the agent reasons over are themselves unreliable: a large-scale study of tool descriptions across the ecosystem found that 97 percent carried at least one quality defect and more than half failed to state their purpose clearly ([Model Context Protocol Tool Descriptions Are Smelly](https://arxiv.org/abs/2602.14878)). And because the namespace is flat, the most common tool name, "search," collided across dozens of distinct servers, which is both an accuracy problem and a security one: a rogue server can shadow a trusted tool by adopting its name and metadata.

The observation worth keeping is that a tool is a capability, and the agent calling it is a consumer with concerns. A tool's description is its claim to address a concern; its parameters are its contract; its server is its provider; its version is its lifecycle coordinate. None of that requires a new metamodel. The same structure that lets a senior leader load many teams' assemblies and see capabilities addressing the same concerns appear adjacent in the merged graph (see [Surfacing duplication across a portfolio](#surfacing-duplication-across-a-portfolio)) lets a platform owner load many servers' tools and see two tools that address the same concern of the same agent appear adjacent, whether or not they share a name. Duplication and ambiguity become structural facts about the catalog rather than incidents discovered at runtime when the agent picks the wrong tool.

The metamodel adds three things the runtime-retrieval approaches that dominate this space do not. First, the catalog is a durable, version-controlled, attributable artifact rather than an ephemeral vector index rebuilt per query, so a tool's provenance, authority, and lifecycle travel with it and a consolidation decision can cite the model. Second, tools cluster into *neighborhoods* of related capabilities that are authored and governed rather than merely inferred from embedding proximity, which gives the boundaries names, owners, and rules. Third, the neighborhood an agent sees can be scoped to the agent: an NSML transformation materializes the slice of the catalog relevant to one agent's task as a [semantic context model](https://medium.com/nasdanika/why-we-need-a-semantic-context-model-ae231d8004e3), so the agent reasons over a small, coherent, unambiguous neighborhood instead of the whole flat list. This is the same scoping mechanism described in [AI as a first-class consumer](#ai-as-a-first-class-consumer), applied to tools rather than to capabilities, and it complements rather than replaces gateway-level curation and runtime retrieval: the gateway decides what an agent is *allowed* to call, retrieval decides what is *surfaced* for a query, and the model decides what is *coherent* to expose in the first place.

### Modeling agent personas: the agent as a first-class persona

Agentic systems are conventionally configured in prose. Each agent receives a system prompt describing who it is, what it pursues, and which tools it may call; the prompt lives in source code or configuration, is maintained by developers, and is invisible to the subject-matter experts whose domain the agent operates in. Vendor guidance and certification curricula now speak of *agent personas* as a design construct - an agent has a role, goals, and a bounded set of tools. The construct is exactly what this metamodel already provides: a persona is whoever holds intent, and nothing in that definition requires the holder to be human. The [personas of this metamodel itself](#personas) list an AI agent among them; this use case turns that observation into a design discipline for agentic systems.

An agent persona is a Persona whose concerns are the goals the agent is built to pursue. The tools it calls are capabilities pulled by those concerns - the same mapping [Governing a catalog of tools an agent can call](#governing-a-catalog-of-tools-an-agent-can-call) establishes: a tool's description is its claim to address a concern, its parameters are its contract, its server or module is its provider. Tool capabilities carry dependencies as first-class structure - a *categorize spending* capability declares its prerequisite on *retrieve transactions* - so the build order and the failure surface of an agent's toolkit are properties of the model rather than tribal knowledge in the orchestration code.

Four consequences follow:

- **The agent's design is published and inspectable.** An agent persona defined on the model can be reviewed, disputed, and maintained by SMEs in the same authoring surfaces as any other persona - not reverse-read out of a system prompt buried in a repository. What the agent is *for* becomes an attributable, versioned assertion with authority and validity, like every other assertion on the graph.
- **Agent personas align to the human personas they serve.** An agent acting on behalf of a customer or an internal role is not a free-floating actor; its goals exist to serve that human persona's concerns. The *supports* relationship from [Federated goal alignment](#federated-goal-alignment-across-teams-and-virtual-organizations) carries this: each agent goal supports one or more concerns of the persona the agent serves, so "why does this agent exist?" is a query, and an agent goal supporting no human concern is a detectable gap - the same class of check as a capability addressing no concern.
- **Tool selection becomes governed rather than accumulated.** Because the agent's toolkit is the capability neighborhood its concerns pull, scoping an agent to a coherent, unambiguous tool set is a transformation over the model rather than a hand-curated list - the mechanism the tool-catalog use case above describes, now anchored to a modeled persona rather than to an ad-hoc task description.
- **System prompts become derived views.** The structural core of a system prompt - who the agent serves, what it pursues, which capabilities it may use and under which dependencies and constraints - is generated from the persona's slice of the model, the same way [persona-anchored positioning](#persona-anchored-positioning) and [persona-tailored executive summaries](#persona-tailored-executive-summaries) are derived rather than authored. The prompt stops drifting from the design because it is a projection of the design. What the model does not carry - tone, style, guardrail prose - attaches to the persona as documentation rather than pretending to be structure.

The payoff is that the gap between an agentic system's *design* and its *configuration* closes. Today the design lives in a deck, the configuration lives in prompts, and the two drift; here they are one artifact and its derived views. An agent is specified, reviewed, aligned, and audited with the same vocabulary, the same federation mechanics, and the same authority model as every human persona and every capability in the portfolio - which also means humans and agents serving the same concerns are directly comparable on the graph, because they are the same kind of element.

### Auditable enterprise architecture

An architecture board needs to demonstrate that strategic capabilities have been allocated to providers, that commitments have authorities and dates, that dependencies are documented, and that retirements have rationales. 
The model is the audit artifact. 
Every assertion has provenance; every lifecycle transition has an asserting authority; every dependency is structural rather than narrative.

The payoff is that audit is a query rather than an interview. 
The model is the same model used for everyday decisions, not a separate artifact prepared for compliance - which means the audit reflects how the organization actually operates, not how it presented itself for the review.

### Synergy discovery

A product portfolio of any size accumulates capabilities at different stages of maturity - some shipped, some in pilot, some in design, some only proposed.
The opportunities that compound value are rarely the individual capabilities; they are the *combinations* - a shipped capability A whose outcomes happen to satisfy a dependency of an in-design capability B, or a pair of early-stage capabilities C and D that, taken together, address a market segment neither was scoped to serve alone.

These cross-capability synergies are systematically under-discovered in organizations of any nontrivial size. 
Not because portfolio owners are inattentive, but because the discovery work scales with the *combinations* of capabilities, not with the count - and human attention does not. 
By the time a portfolio has dozens of capabilities across multiple maturity tiers, the space of plausible synergy candidates is larger than any review meeting can cover.

The product management model addresses this by making the portfolio machine-readable.
Each capability declares its outcomes, dependencies, beneficiaries, and current maturity.
An AI agent traversing this graph can systematically enumerate candidate synergies - combinations of capabilities whose outcomes satisfy each other's dependencies, whose beneficiaries overlap, or whose composition opens a use case neither member supports alone.

The traversal is configurable along the dimensions that matter to the reviewer:

- **Maturity band.** Restrict the search to shipped-only synergies (near-term opportunities), or open it to early-stage capabilities (strategic foresight). A common pattern is two passes: one over available capabilities for immediate roadmap input, one over the union of available and proposed capabilities for portfolio-shaping decisions.
- **Search depth.** Pairwise synergies are cheap to enumerate; three- and four-way compositions grow combinatorially but often surface the most interesting candidates. The agent can be bounded by depth, by predicted value, or by a budget.
- **Scope.** A portfolio owner reviewing a single product line will scope the agent to that line; a CTO reviewing the company portfolio will not.

The agent's output is a ranked list of synergy candidates, each with a traceable rationale grounded in the model - which capabilities are involved, which of their declared outcomes and dependencies align, and which maturity transitions would be required to realize the synergy.
The artifact is *evidence for a human decision*, not an autonomous portfolio change.

The value is asymmetric: discovery scales beyond human attention, while the decisions remain with humans informed by candidate proposals they would not have generated on their own.

The economics underneath this are worth making explicit, because they have changed.
The agent is only as useful as the field of candidates it has to traverse, and that field is a function of how much of the portfolio has been captured in legible form.
Capturing a capability - modeling its outcomes, dependencies, and beneficiaries - was historically expensive enough that it was done only for things with a clear present need; speculative capture could not be justified.
Generative authoring collapses that cost. When turning something one already understands into a legible model costs an afternoon, the threshold to capture falls below the threshold to *decide whether capture is worth it*, and authors begin to record capabilities they have no immediate use for.

This matters because the highest-value combinations are, by definition, the ones nobody planned.
One reason the web became generative was *unexpected reuse* - a resource published for one purpose recombined for purposes its author never imagined - and unexpected reuse is possible only when the thing is addressable and legible before anyone knows what it is for.
The same holds here: a capability captured speculatively, with no synergy in mind, is precisely the kind of element the agent can later find a synergy *for*.
Cheap, speculative, legible capture is what populates the graph densely enough for discovery to pay off; composability is what converts that density into found value rather than accumulated clutter.
The benefit does not require generated code or even a design document - legibility alone is enough for both the author who no longer has to re-derive the thing and the agent that can now reason over it.

### Proactive capability construction within a budget

The use cases so far treat agents as advisors: they discover, propose, validate, and assemble, but a human makes and executes the decision. The *commit → evaluate-if-enough-input → act* pattern from [Waypoint](https://github.com/Nasdanika/waypoint) raises the ceiling. A coding agent given an *exploration/building budget* - a bounded spend, say $100 a month - watches the federated model for capabilities whose intent is articulated densely enough to build, and builds them, unattended, when the input is sufficient.

The mechanism is the persona-pull pipeline run by an agent rather than a human. The agent traverses persona → concern → capability to find the highest-value capability whose concern is real and whose provider does not yet exist, then evaluates whether the capability is specified well enough to implement: are the requirements and design present, are prerequisites available, is the scope bounded? If yes, it acts - generates the capability provider (code, model, loader), attaches it to the capability with provenance, and records the spend against the budget. If not, it does not guess; it escalates (below).

A recent build followed exactly this shape by hand. The [SQL AST model](https://sql.models.nasdanika.org/ast/) began as a concrete need - analyze SQL statements - and from that point the work was mostly generative: survey prior art, design the AST metamodel, generate the code, produce a loader. Had the need been modeled as a capability with its concern attached, the same sequence could have run in the background: an agent prioritizing by persona → concern → capability would have selected "analyze SQL statements," found no provider, judged the requirement specific enough, and produced the AST model and AST loader as a capability provider - the human reviewing the result rather than driving the construction.

The consequence that makes this compound is that *building one thing unlocks the next*. A delivered capability provider lowers the bar for the capabilities that depend on it, and the agent re-evaluates the frontier (see *Graph-driven portfolio visualization and build-order navigation*) after every commit. The unlock is frequently across an authority boundary, and this is where the *evaluate-if-enough-input* step becomes a collaboration protocol rather than a yes/no gate. Suppose Jim elaborates capability provider A - a SQL AST loader. Its existence lowers the bar to build an *automated SQL review* capability, but that capability is under-specified: there is not enough input to build it. The system identifies Joe as the persona who holds the concern automated SQL review would address, and emails him asking him to elaborate his design so agents can build on it. A lighter variant inverts the default: the system informs Joe that it *can* build the capability on the current input and asks for confirmation - Joe either confirms, or elaborates and confirms. Either way the missing input is requested from precisely the person whose intent supplies it, and the build resumes from the same artifact when the input arrives.

The escalation channel is governed by human preference and back-pressure, not by the agent's appetite. A person controls the demand they receive: Joe may opt out of elaboration notifications or of proactive building against his concerns entirely; he may cap the rate ("at most one request per week"); and the system respects the requests already in flight to him - if he has a few pending, it does not pile on. This is the same reactive-streams back-pressure the executive summary invokes, applied to human attention: the consumer signals how much demand they can absorb, and the producer - here an agent - throttles to match. Requests also expire. A confirm/elaborate request carries a deadline - say two days - after which it times out and the candidate is released rather than left blocking the queue. When a request to Joe is declined, throttled, or timed out, the agent does not stall; it re-prioritizes to whatever it *can* act on. If Joe is opted out or overwhelmed, Jane's concerns get addressed instead - capabilities built against her intent even though their nominal priority is lower than Joe's. Progress follows the art of the possible: the agent maximizes value actually delivered given real human availability, rather than idling on the highest-priority item whose owner is unreachable.

The payoff is that the model becomes a standing work queue for autonomous construction, bounded by an explicit budget and gated by sufficiency of intent rather than by a human's availability to start each task. Speculative, legible capture (see *Synergy discovery*) is what keeps the queue full; the persona → concern → capability structure is what lets an agent prioritize it; the budget is what keeps proactive building safe; and the escalate-for-elaboration step is what turns "not enough information" from a dead end into a routed request to the one person who can resolve it. Demand and supply meet on the model, and supply can now move first.

### Pull-driven inquiry and discovery

Most product communication is push: vendors and internal teams describe their offerings on their own terms, in their own sequence, at the level of detail they choose. Consumers receive whichever narrative reached them first or was packaged most attractively, and triangulate among them on their own time.

The model supports pull. A consumer articulates a concern - a goal, a need, a pain point - and the capabilities that address it surface from the federated graph with provenance, evidence, dependencies, lifecycle state, and asserting authority. The order of presentation is driven by structural fit rather than by which capability owner had the better speaker on the day.

The payoff is that discovery becomes consumer-led. Capability owners no longer compete for attention; their capabilities are found when, and because, their addressed concerns match what a consumer is asking for. A capability that addresses a real concern is discoverable even if its owner is not the loudest voice in the room. The pattern mirrors backward chaining in inference systems and reactive streams with back-pressure - structurally well-understood inversions of the push default.

### Briefings calibrated to the audience's level of abstraction

A familiar friction in management briefings: a senior stakeholder asks for an update, the engineer simplifies the proposal to fit the available time, the stakeholder hears "this is simple" and asks why it is taking so long, the engineer begins explaining the detail that was simplified out, and the conversation degrades on both sides. The underlying problem is not that either party is wrong - it is that "WHAT" and "HOW" live at different levels of abstraction, and the same artifact rarely serves both audiences well.

The model separates these layers structurally. A senior stakeholder operates at the persona-concern-capability layer; a delivery team operates at the capability-provider-dependency layer; an architect operates across both. The same model serves all three views, with each view derived rather than re-authored. A trusted "WHAT" answer for an executive does not require flattening the underlying detail - it sits one link above it.

The payoff is that briefings stop being lossy. A summary assembled from the model presents the layer appropriate to the audience, with traceable links into the layer below for any reader who wants to descend. The "if it is so simple, why is it taking so long?" misreading does not arise, because the simpler view is *visibly* the head of a deeper structure rather than a claim that the work itself is small. Trust between layers is sustained by the structural integrity of the model rather than by the rhetorical skill of whoever happens to be presenting on a given day. The pattern is particularly valuable for managing senior contributors whose judgment is grounded in detail they cannot easily articulate on demand - the *coup d'œil* of an experienced engineer becomes legible to a manager who is not expected to share that intuition.

### Eliciting requirements against the curse of knowledge

A business leader hands a development team three lines of requirements and expects a result in days. The team starts digging and finds that the three lines rest on a body of knowledge the leader holds so deeply they no longer know they hold it - the *curse of knowledge*, the "you don't know what you know" level where one assumes everyone shares one's own context. Beneath the terse ask sit several people who each report to the leader and each own one fragment of the real problem, expressed in incompatible terms: one tracks Mars bars in boxes, another tracks Snickers bars in centners. The leader sees an easy request; the team sees an unbounded one. Neither is wrong, and that is the trap - the leader cannot understand why something so obvious takes so long, the team cannot deliver against a specification that does not exist, and the same misread runs one level up: the leader's own superior expects reporting the leader cannot produce and concludes the leader is uncooperative. The person doing the elicitation, caught between an unstated need and the people who hold its pieces, absorbs the blame for a gap that is structural rather than personal - and is sometimes let go for it.

The model attacks the gap at its source by making the implicit explicit as a condition of capture. The leader is - or owns - a persona; the three-line ask is a need; from that need a capability graph is built, with AI assistance, decomposing the ask into the concerns it actually entails and the capabilities each would require. The decomposition is the act of surfacing the hidden knowledge: it forces the questions the curse of knowledge suppresses - whose concern is this, what does "the report" actually measure, in what unit, held by whom? The reports who own the fragments become capability providers or persona owners in their own right, and their incompatible representations - boxes, centners - stop being tribal knowledge and become declared, reconcilable structure, the same semantic-reconciliation problem NSML is built to express. Some capabilities the decomposition surfaces may already exist in the model in Proposed or Elaborated state; the leader's ask is the pull that funds their construction, and once built they serve other personas who share the same underlying concern.

The payoff is that elicitation stops being a contest over who is being unreasonable. The terse ask and the detailed reality become two ends of one structure rather than two parties talking past each other, and the WHAT/HOW abstraction gap that *Briefings calibrated to the audience's level of abstraction* closes on the way down is closed here on the way up. The blame the elicitor would otherwise absorb is replaced by a graph that shows precisely what the ask entails, what is already available, and what remains to be built - legible to the leader, to the reports, and to the leader's own superior alike.

### Decisions grounded in evidence rather than opinion flow

In organizations where shared structural representation is absent, information about products, vendors, and approaches propagates through informal channels: someone read an article, someone heard at a conference, someone in authority mentioned a name in passing. These channels carry real signal, but the signal is opinion-shaped and authority-shaped rather than evidence-shaped. A claim that "everyone is moving to X" is indistinguishable, in the absence of structure, from the original source being a single trade publication or a single conversation. The dynamic is analogous to chemical signalling in a microbial mat - informational, but undirected and lossy compared to a nervous system.

The model replaces opinion flow with structural reasoning. A candidate solution - whether surfaced through informal channels or proposed internally - is evaluated against the persona concerns it would address, the evidence supporting those concerns, the dependencies and prerequisites it carries, and the authority making each claim about it. "We should adopt X" becomes a query with an auditable answer rather than a social statement.

The payoff is not that informal channels disappear - they will not, and should not - but that proposals reaching the decision point arrive there with their evidentiary basis attached. The trail from "we chose X" back to "because persona Y's concern Z is supported by capability X's claim W, asserted by authority V" is queryable. Choices made on weaker grounds remain visible as such, and the unspoken weight previously carried by who-said-it is rebalanced against what-can-be-shown.

### Standardized proposal format across organizational boundaries

A familiar pattern in enterprise procurement and partner onboarding: a proposer produces a deck or document, the receiving organization reads it, the receiver's team translates the document into their own internal evaluation format, and the translation step is where most of the work - and most of the information loss - happens. The document is shaped to the proposer's narrative; the receiver's framework is shaped to the receiver's concerns; the staff who do the translation absorb the impedance mismatch. Comparable proposals from different proposers arrive in incomparable shapes, and the comparison work begins with reshaping rather than with comparing.

The model supports an alternative: the receiver stipulates that proposals be submitted as a product management model - personas, concerns, capabilities, evidence, dependencies, lifecycle state. The analogy is the resume-format requirement that some employers post: arriving in the requested format is what gets the proposal considered sooner, or considered at all. A receiver who has already articulated their own personas and concerns has, by virtue of that work, a graph to match incoming proposals against. The match is computable - which of the receiver's concerns the proposer's capabilities address, which are unaddressed, which of the proposer's capabilities address concerns the proposer did not realize the receiver had - AI-assisted in the first pass, validated by humans in the second. The last of these is often the most interesting case and the one a prose comparison is least likely to surface: a capability whose stated purpose differs from the use the receiver would actually put it to.

The pattern works in both directions. A receiver may stipulate the format; alternatively, a proposer may publish its product management model at known Maven coordinates and let receivers pull it. Either side can flip the asymmetry - whoever moves first reduces the translation cost for everyone downstream. Partner relationships work the same way: large enterprises with many integration partners (a bank whose internal capabilities are tribal knowledge and whose partners spend months learning what is available is the canonical example) can publish a partner-facing model and let partners discover capabilities by querying it rather than by negotiating access through human gatekeepers. The model becomes the contract surface for the relationship - durable, citable, and amenable to AI-assisted navigation that human onboarding cannot match in throughput.

The internal variant is even cleaner. An innovation program that accepts ideas as free-form documents pays the conversion cost on every submission. If the program stipulates capability format up front - "submit your proposal as a capability declaration referencing the persona and concern domains we already publish" - the submission federates directly into the portfolio model. A reference added to a domain YAML file is the entire intake step. Ideas reach the evaluation stage in the same shape as the capabilities they would join, removing the lossy translation that otherwise drops the most precise framing on the floor.

The payoff is that organizational boundaries stop being shape-changing operations on the information that crosses them. A vendor proposal, a partner-onboarding submission, and an internal innovation idea all arrive on the same graph the rest of the model lives on. Comparison, matching, and federation are operations over a single structure rather than a sequence of human translations between incompatible formats.

### Federated goal alignment across teams and virtual organizations

Strategic alignment - the property that every individual's work traces back to a customer concern - is what every organization claims to want and few achieve.
The usual failure mode is that goals are set in tools that do not compose: a team's OKRs live in one quarterly document, the department's strategy lives in a slide deck, the company's mission lives on a poster,
and the actual customer concerns that motivate everything live nowhere structured at all.
Tracing the line from *"I am working on this today"* back to *"because this customer has this concern"* requires reconstruction from heterogeneous sources, and the reconstruction is rarely done.

The model supports alignment as first-class structure. The same Concern type that captures customer-persona goals captures organizational goals, team goals, and individual goals - the persona is just whoever holds the concern.
A *supports* relationship at the Concern level lets a goal support another goal (a team goal supports a department goal) or any other concern type (a team goal addresses a customer need or pain point).
The alignment is a directed graph over concerns, queryable from any node: *"what supports this goal?"* and *"what does this goal ultimately serve?"* become single queries rather than multi-tool reconstructions.

Hierarchical organizations whose alignment is partially fictional benefit from keeping alignment narrative-shaped rather than queryable; making it queryable exposes the fiction.
That is not a flaw of the model; it is the property the model provides.
Organizations that already align well lose nothing; organizations that do not lose narrative cover.

For virtual organizations - open source communities, federated partnerships, multi-org research consortia, distributed engineering teams that span employers - it works precisely because no central HR tool serves the population.
Each contributor publishes their own goals in their own repository under their own authority; the supports relationships make the alignment visible without anyone administering it;
agents reasoning over the federated graph can answer alignment queries the same way they answer capability queries.

The Nasdanika ecosystem itself is the worked example. The umbrella publishes a goal - *agents that operate on federated models with audit and access control* - and the semantic mapping language, the capability framework, the product management model,
and the execution components each publish their own goals that reference and support the umbrella goal through their own concern declarations.
The alignment is visible to anyone who loads the federation; no central project manager curates it; it stays accurate as the repositories evolve because each repository owns its own goals.
The umbrella goal can itself reference a customer concern - *developers and architects need their AI assistance to be grounded, verifiable, and bounded by their access policies* - and the chain from any individual
contribution back to the customer concern becomes a query rather than a reconstruction.

The pattern transfers wherever virtual organization exists. Cross-employer open source projects align contributor work to project goals to user concerns.
Multi-vendor partnerships align partner roadmaps to joint customer accounts. Internal innovation programs that span business units align proposal goals to portfolio-level concerns.
In each case the property that makes the alignment possible is the same one that makes the capability federation possible: artifacts that compose by reference rather than by central administration.

### Methodology-neutral goals: OKRs, V2MOM, and other frameworks as extensions

Several goal-setting methodologies compete for primacy in industry practice - OKRs, V2MOM, North Star, Hoshin Kanri, MBO, and many local variants.
Each has advocates; each is contested by adherents of the others; organizations that adopt one frequently abandon it for another after a few years.
The methodology choice is real and consequential, but it is also tactical - the structural primitives the methodologies rest on (goals, supporting goals, measurable outcomes, owners, time bounds) are common across all of them.

The core model is methodology-neutral. The Goal concern type, the *supports* relationship, temporal validity, and authority on every assertion together provide the structural primitives every named methodology needs.
A specific methodology is a sub-model that extends the core. An OKR extension introduces Objective as a specialization of Goal - with the additional constraint that it has measurable Key Results attached as Evidence and a defined period of validity.
A V2MOM extension introduces Vision, Values, Methods, Obstacles, and Measures as specializations of the same primitives.
An MBO extension introduces what its practitioners would expect.
Each methodology becomes a Maven artifact organizations choose to adopt without forcing every adjacent organization onto the same one.

The payoff is twofold. The first is that organizations can pick a methodology that fits without losing the ability to federate with others who picked differently.
An OKR-adopting team and a V2MOM-adopting team that need to align across organizational boundary can do so at the core-Goal level, where both methodologies meet, without either team having to migrate.
The supports relationship works across methodology boundaries because both methodologies' specializations inherit from the same root concept.
The second is that methodology choice stops being a strategic commitment that requires re-platforming - switching from OKRs to V2MOM is a matter of loading a different extension model, not of rebuilding the goals infrastructure.
The substrate persists; the methodology is a thin layer above it.

The pattern is the same one the model uses for prioritization frameworks. RICE, WSJF, AHP, and other prioritization methods extend the core capability model the way OKRs and V2MOM extend the core concern model.
The framework's value is in being a substrate other people's structured thinking can attach to, not in committing to any one school of structured thinking itself.
Organizations bring their methodology; the substrate carries their methodology's structure as one extension among several rather than as the only legible shape.

### Symmetric evaluation of vendor and internal offerings

A pattern recognizable in any organization with hierarchical accountability: in a meeting attended by peers and a manager, it is politically safer to propose a solution from a large external vendor than a comparable proposal from one's own team. The vendor proposal carries the implicit endorsement of the vendor's market position; the internal proposal carries the explicit endorsement of the proposer, with the associated career risk if the proposal does not work out. The asymmetry biases selection toward external offerings independent of fit. A solution that is a poor match for the concerns at hand can still be the preferred recommendation, simply because recommending it costs the recommender less.

The model represents both candidates in the same shape: personas addressed, concerns addressed, evidence supporting each address, dependencies and prerequisites, lifecycle state, asserting authority. A vendor offering becomes a capability provider on the same graph as an internal team. A senior leader reading the model sees both candidates on the same axes, with fit, evidence, and dependency footprint visible structurally rather than narratively.

The payoff is twofold. The first is local: the choice between candidates is made on documented fit rather than on the perceived safety of proposing one or the other. The second is structural: the political cost of endorsing an internal proposal is reduced because the endorsement is by reference to the model rather than by personal vouching. A manager defending an internal proposal does not have to argue against the vendor's market presence; they reference the rows in the table where the internal proposal addresses concerns the vendor does not.

The same mechanism raises the ceiling on what an organization can collectively endorse. When an internal team's work is opaque to the next layer up, that layer becomes the de facto upper bound on what gets recognized - the team's contribution is capped at its manager's ability to articulate it. When the work is legible in the same model the senior layer already uses to view the vendor landscape, the team's capabilities are evaluated on their own terms rather than filtered through any single individual's understanding of them.

### Persona-anchored positioning

Considerable effort in product organizations is spent authoring positioning statements and then defending them. The work is usually anchored to a market narrative - "we are the X for Y who want Z" - and lives in slide decks that drift out of sync with the underlying product over time. The positioning is then re-stated, re-debated, and re-published, often without the underlying capabilities changing.

The model treats positioning as a derived view rather than an authored artifact. A persona asking "what is this capability for?" receives an answer assembled from the model: which of that persona's concerns the capability addresses, what evidence supports each address, which alternatives exist in the federation, where this capability sits in lifecycle, what dependencies it carries. Two personas asking the same capability the same question can receive different, correct answers - because the capability legitimately means different things to different consumers, and the model captures both meanings rather than averaging them into a single statement that fits neither.

The payoff is that positioning stops being a maintenance burden. The capability's positioning toward any persona is whatever the model says about it at that moment. When the capability's concerns, evidence, or dependencies change, every persona-anchored view of it updates without anyone editing a deck. Positioning becomes pull-driven for the same reason discovery does: the consumer's persona, not the producer's narrative, is the index into what is said.

### Persona-tailored executive summaries

A consistent observation in capability development is that the people building a capability are usually the wrong people to write its executive summary. They know it too well to compress it; they explain its mechanism when an executive needs its outcome; they over-qualify and under-state. The result is summaries that fail the audience they were written for.

An LLM with access to the model can produce executive summaries the capability owner cannot. The summary is assembled from the persona-concern-capability graph and the evidence attached to it, not from the owner's intuition about what should be said. Citations attach to specific elements at specific versions, so the summary remains verifiable.

The advanced form is persona-aware: the same capability produces a different summary for a senior leader (portfolio context, consolidation candidates, evidence of adoption), an architect (dependency footprint, lifecycle interactions with adjacent capabilities), and a delivery lead (commitments, prerequisites already available, remaining gaps). The summary is calibrated to what the requester's persona cares about rather than to a generic audience.

The payoff is informational: stakeholders read summaries that address their concerns at their level of abstraction. There is also a softer payoff that organizations will recognize: the friction in a contributor explaining their own work to a senior stakeholder - the implicit status negotiation that often colors such exchanges - is replaced by a generated artifact whose tone is uniform regardless of who asked. The conversation that follows is about the content rather than about the contributor.

### Persona-scoped change feeds

The model is under version control. Comparing two points in its history produces a structural diff: capabilities added or withdrawn, lifecycle states moved, dependencies introduced or removed, authorities asserting new positions. The diff is precise because the model is precise.

An agent translates that diff into change feeds scoped to a persona, a persona domain, or a capability domain. A subscriber receives notifications limited to changes that affect them, with the impact written in their terms. An enterprise architect sees lifecycle moves and dependency churn; a platform consumer sees commitments that affect downstream plans; a senior leader sees consolidation candidates and adoption shifts; a capability owner sees citations into their own capability from elsewhere in the federation.

The payoff is that consumers stay current without subscribing to everything. The model already knows what each persona cares about; the feed is a query over the diff filtered by the persona's concerns. Compared to broadcast announcement channels - which everyone eventually tunes out - the persona-scoped feed carries signal that compounds with use, because each notification arrives already framed in terms of the consumer's own goals, needs, and pain points.

### Confidential strategy work within a federated model

The federation described so far is openly readable - every assertion is attributable, citable, and consumable by anyone who imports the artifact. That property is desirable in many settings and inappropriate in others. Senior leadership reasoning about an upcoming acquisition, an organizational restructuring, a divestment, or a sensitive customer engagement cannot publish those concerns into a model the whole organization will read. In simple cases the consideration is held in a prompt or a slide deck and never enters the model at all; the reasoning that depends on it becomes opaque, and the model loses the very assertions that drive the largest decisions.

The model supports confidential reasoning through transformation-based access control. The full federation is assembled in a protected environment - private repositories, on-prem stores, or local models that reference external public artifacts. An NSML transformation expressing the access policy of a particular audience produces the view that audience is permitted to see; elements and assertions outside the policy are not present in the produced view. The produced view is a self-contained model that flows through the same tooling as any other Maven-published model.

The payoff is that confidentiality stops being a reason to exit the model. Senior leadership can articulate concerns in the full federation that the broader organization will not see. Hidden-agenda reasoning becomes structured, citable, and auditable within its permitted audience rather than informal and unrecoverable. The "publish without permission" property is preserved - an author can still publish a concern - but is now per-audience rather than all-or-nothing, so dissent can be voiced inside an appropriate circle rather than forced into either the public square or silence.

Access control is also what makes agentic execution safe in confidential environments. An agent given a transformed view cannot reason over elements the transformation removed; the boundary of what the agent sees is identical to the boundary of what the user sees, by construction. Confidentiality is a property of the view, not a property the agent has to be trusted to respect.

### Multi-tenant persona portals and partner-specific views

An organization serving multiple business customers, partners, or internal lines of business often needs to publish capability information differently to different audiences. The same capability has different positioning, different evidence relevance, and different commitment context for each audience; some elements of the federation are specific to a single audience and must not leak to others.

The model supports this through audience-scoped transformations. An organizational federation contains the union of capability declarations, persona definitions, evidence, and commitments. A per-audience NSML transformation produces the view that audience is entitled to consume - their own personas and concerns plus the shared capabilities of the organization, with confidentiality enforced structurally. Each audience receives a coherent, self-contained federated view; cross-audience leakage is impossible because the elements were never composed into the transformed view to begin with.

A particular pattern this enables is the self-service persona portal: each consumer authors their own personas and concerns inside the portal, interacts with the model to find which shared capabilities address them, and never sees the persona or concern definitions of other consumers. The shared capabilities are common; the consumer-specific personas are private. The same mechanism that gates senior-leadership confidentiality gates per-tenant isolation, and the policy in both cases is an auditable NSML transformation rather than tribal knowledge about who is allowed to see what.

### Interactive consultation through agent-backed operations

A capability declaration on the model carries data: the concerns it addresses, its evidence, its dependencies, its lifecycle. A consumer reasoning over the model often wants more than data - they want to ask: "summarize this for my role," "find capabilities like this one for a different persona," "compare these three proposals on the dimensions that matter to me," "explain what changed since last quarter relative to my concerns."

The model exposes these interactions as **operations** declared on the view rather than as freestanding chat conversations alongside it. An operation is part of the contract the view defines: it has a name, a typed signature, an access policy, and a backing implementation. The backing may be a hand-written service, a workflow, or - increasingly - an agent that operates over the same view the user is consuming.

The payoff is that interactive consultation is grounded in the same structural model as the rest of the consumer experience. Conversations become invocations: each agent call has typed inputs and outputs, the view bounds what the agent can see, and the operation can be cited the same way an attribute or assertion can. Agentic execution stops being a parallel channel and becomes a first-class feature of the model the consumer is already reading. Combined with audience-scoped views, agent-backed operations make the model itself the interaction surface: data plus behavior, both bounded by the same access policy, both authored as part of the same transformation.

### Crowdfunded prioritization of shared capabilities

In organizations with shared services, capabilities that benefit many teams but belong to none face a familiar funding problem. No business line wants to budget for a capability whose consumers sit largely elsewhere; the beneficiaries each rationally prefer that someone else funds it. The pathology - tragedy of the commons, infrastructure under-investment, "shared services means shared underfunding" - persists because the usual remediation requires central authority and visibility the federated model does not assume.

The model supports an alternative: a credit-based marketplace layered on the capability graph. Consumers acquire credits - by purchase, by contribution of work, or by other organizational mechanisms - and allocate them to capabilities they want built. When the credit balance committed to a capability reaches the threshold its owner has posted, construction begins. Allocations are *binding* once made; the commitment of real resources is what distinguishes a market from a poll.

The non-obvious mechanism is in the dependency graph. A capability that depends on an unbuilt foundation propagates a fraction of its committed credits upstream to the foundation, weighted by the strength of the dependency. A consumer funding the feature they actually need contributes, by construction, to the infrastructure that feature requires - *without anyone advocating for the infrastructure separately*. Shared services accumulate credits as a side effect of demand for the capabilities built on top of them. The pathology where no one wants to fund the foundation directly is structurally circumvented: the foundation is funded by everyone whose feature depends on it, in proportion to how much they depend on it. The graph context is what enables this. General-purpose crowdfunding platforms cannot do it because they have no model of how the funded objects relate to each other; the capability marketplace can because the relations are first-class on the model the marketplace runs over.

Contributors who deliver capabilities can be paid in credits redeemable for premium features. The flow closes: consumers with concerns acquire credits and direct them at capabilities; builders earn credits by delivering capabilities; credits buy access to premium features whose value anchors the system. Non-fungible attribution - founding-funder recognition, per-capability contributor credit - is a separate object from the fungible voting currency and is best modeled separately.

Several design questions are open and resolved per deployment rather than baked into the metamodel. Sizing capabilities in credits is left to the capability owner; market discipline emerges as consumers decide whether to fund at the posted price. Conversion between credits and money is local: in a corporate context credits may attach to internal budgets, while in an open community they may be sold for cash. All-or-nothing funding with explicit refund or expiry rules is cleaner than partial funding. Sybil resistance, conflict-of-interest disclosure, and the cascade weight on each dependency edge are policy choices the deployment makes, not mechanisms the model imposes. The marketplace requires liquidity to function and is therefore a *late* feature of the platform - useful once a meaningful population of users and capabilities exists to give votes weight, not before.

The payoff is that prioritization stops being a top-down allocation question and becomes a market over the capability graph - with the graph's structure (dependencies, beneficiaries, lifecycle states) doing work that pure crowdfunding cannot do, because pure crowdfunding has no model of how the funded objects relate. Demand and supply meet on the same artifact that describes both.

### Curated catalogs and a marketplace of evaluations (the Gartner pattern)

Everything to this point treats the model as an artifact an organization keeps for itself. But a curated, evidence-backed catalog of capabilities and capability providers is also a product in its own right, and there is an established business selling exactly it. Gartner, Forrester, and their peers sell curated evaluations of vendors against criteria: Magic Quadrants, Waves, scored comparisons. The model expresses that natively. Capability providers, the concerns they address, the evidence behind each claim, and the criteria themselves are already first-class, and a criteria hierarchy is a decision-analysis facet of the same graph, the kind of weighted, multi-level structure [AHP and collaborative decision analysis](ncda.md) operate over. A curated catalog, or a single partial evaluation such as a comparison of two products against one buyer's criteria set, is therefore a publishable, citable artifact rather than a slide deck. The analyst's deliverable becomes a model the buyer can load, query, and recompute against their own weights.

Two delivery shapes monetize this, and both are mechanisms the [feature-level encryption and signature capability](encryption-and-signatures-capability-design.md) already provides rather than new infrastructure. The first distributes premium content as a separate artifact to paying subscribers, the familiar two-edition split. The second is more interesting: publish one artifact with the premium regions encrypted, reified on the consumer side according to the keys the reader holds, so the free catalog and the subscriber catalog are the same file at different resolutions. Either way the artifact is signed, including as a signed Maven artifact, so a buyer can verify that an evaluation is authentic, was published by the analyst it claims, and has not been altered in a repository or in transit. Non-repudiation is what makes a paid evaluation trustworthy: the value of an analyst's rating is precisely that it is attributable to the analyst and cannot be forged.

This opens a marketplace, not just a storefront. Multiple parties publish catalogs and partial evaluations over the same metamodel, some open, some encrypted, and a consumer assembles a federated view across providers the same way they federate internal team models. Because provider, evidence, authority, and criteria are all first-class, the consumer sees who evaluated what, on whose authority, and against which criteria, and the comparison is computed over a shared representation rather than narrated in prose, which is what separates this from a review site. A buyer can take three vendors' self-published capability declarations, one independent analyst's scored evaluation, and their own weighting of the criteria, and resolve a recommendation that cites every input at its version.

Subscriptions can be managed by the publishing party or by the marketplace operator (Nasdanika), and the client supports both as long as billing runs over a common rail such as Stripe. A subscription maps to group keys, and different subscriptions decrypt different regions of the federated model, so entitlement and the ability to read are the same fact rather than two systems to reconcile. The model is one; a subscription opens a slice of it. The same NSML transformations that enforce [confidential strategy work](#confidential-strategy-work-within-a-federated-model) and [multi-tenant portals](#multi-tenant-persona-portals-and-partner-specific-views) express what each subscription tier is entitled to see, so the access policy is an auditable artifact rather than server-side configuration in a vendor's portal.

The payoff is that an analyst-style research business and a federated content marketplace run on the same substrate an organization already uses internally. The curated catalog is not a separate content system; it is the model, signed and selectively encrypted, sold through a billing rail that maps subscriptions to keys. Like the crowdfunding marketplace above, this is a late, ecosystem-stage feature that needs a population of publishers and buyers to be worth standing up, and several questions are policy choices a deployment makes rather than mechanisms the model imposes: revenue split between publishers and the marketplace operator, key rotation when a subscription lapses, and how independent evaluations are attributed and disputed. The mechanism is in place; the market is what has to be grown.

## Synergy versus competition

A recognizable industry-scale pattern: a problem domain develops, multiple vendors or open-source projects produce solutions, each with its own API and conceptual model. Consumers face vendor lock-in; vendors compete on incompatible terms; the comparison happens in marketing material rather than against any shared representation.
JDBC versus proprietary database APIs in the late 1990s, JMS versus native broker APIs in the 2000s, GraphQL versus REST conventions in the 2010s, OpenAI versus Anthropic versus Google response formats today - the same shape recurs across decades, with the same costs.
When a standard finally emerges, vendors initially resist it because of the inevitable impedance mismatch - IBM MQ is not very JMSy, and many of its useful idiosyncrasies have to be smuggled across the standard's surface as out-of-band properties.
The standard wins partial adoption; vendor-specific extensions remain for the cases the standard does not cover well.
And by the time one standard achieves traction, several others have appeared in adjacent domains - the joke Andrew Tanenbaum is credited with, *"the good thing about standards is that there are so many to choose from,"* captures the failure mode precisely.

The same dynamic shows up at smaller scales inside organizations.
Two individuals or two teams independently arrive at similar product ideas, or propose capabilities that overlap in scope.
The default outcome is competitive: each side defends its proposal by surfacing weaknesses in the other, resources and attention flow to whichever proposal survives the comparison, one "wins"; the other is shelved, often with the loser's underlying insight lost to the organization.

In both shapes - cross-vendor at industry scale, cross-team at organizational scale - the pattern persists because the alternative is expensive.
Structured comparison and reconciliation require a shared representation.
Without one, two proposals are two narratives, the comparison is rhetorical, and rhetorical comparisons reward defensive posture over informational completeness.

The product management model changes the medium.
Both proposals - whether they are two competing vendor offerings or two competing internal capabilities - enter the model as candidates with explicit scope, outcomes, dependencies, target beneficiaries, and assumed operational context.
Overlap and divergence are no longer assertions to be argued; they are properties of the model that can be computed and displayed.
The model does not try to *be* the One True Standard.
It is a way of producing *many cheap standards* - formal authoritative definitions of personas, concerns, capabilities, and providers, versioned and federated in a graph of relationships, attributable to their authors, citable across boundaries.
Consumers reason about which to follow based on fit: JMS or AMQP for simple messaging where the standard's coverage suffices, vendor-specific APIs for deep integration the standard does not express well, and a structured graph showing where each strikes the trade-off rather than which one is "best."

Three consequences follow:

* First, leadership and consumers see the comparison in the same form they see the rest of the portfolio. The decision input is structural, not rhetorical: where the proposals overlap, where they diverge, which dependencies they share, what outcomes each uniquely contributes. AI assistance over the model can synthesize this view on demand and answer follow-up questions ("which of these proposals' dependencies are satisfied by existing capabilities?") without re-litigation.
* Second, the disagreement itself is preserved as documented context. A decision to advance one proposal and pause the other is reversible in a meaningful way, because the rejected proposal is not lost - it remains in the model with its scope and rationale intact, available when context changes. Yesterday's "losing" vendor API may be tomorrow's best fit for a workload the original comparison did not anticipate.
* Third - and this is the outcome that is hardest to achieve in the rhetorical mode - the proposals' authors often discover that their offerings are *complementary* rather than competing. Two capabilities that looked like duplicates in a slide deck frequently reveal, once decomposed into outcomes and dependencies, that they address different beneficiaries or different stages of the same user journey. The model surfaces this. The collaboration that becomes possible at that point - defining the two capabilities as a coordinated pair, with explicit interfaces - is a structurally better outcome than either proposal alone. JMS and AMQP are not really substitutes for each other once their actual scopes are made structural; the same is true of most "competing" internal capabilities once the model exposes their actual scopes.

The use case does not claim to remove all competition - between vendors, between teams, between offerings.
Some proposals genuinely conflict, and the choosing party must choose.
The claim is narrower: that the structured-model representation moves the conflict to terrain where the decision can be made on documented analysis, and where collaboration - or the lighter-weight outcome of clearly bounded coexistence - is at least *available* as an alternative to the winner-takes-all rhetorical mode.


The section so far assumes the proposals already exist and asks how to compare them. Generative AI changes the prior step.
When producing a working artifact - a metamodel, a loader, a running prototype - costs hours rather than quarters, the economics that justified deliberation invert.
Deliberation was the cheap part when building was expensive; you argued about which thing to build because building was the irreversible cost.
Now it is frequently cheaper to build both candidates and compare the artifacts than to litigate them in the abstract. Humphrey's principle -
that people do not know what they want until they see what they don't - stops being a reason to tolerate slow discovery and becomes an
instruction: do not argue value, produce the thing and let recognition happen.

Organizations keep gating as though building were still expensive.
The approval gate, not the build, becomes the bottleneck - and a gate measured in weeks on an artifact measured in hours is a legible signal in
its own right that the gate is protecting sunk reputation rather than managing risk.

The deeper failure is that a cheap artifact is *illegible* without a coordinate system.
With nowhere to land, a newly created capability reads as a threat to whoever has invested in the incumbent and as noise to the leadership that cannot place it.
This is the same rhetorical-versus-structural divide as before, but it now binds at the moment of creation rather than the moment of comparison.
The model is what converts a cheap artifact from threat-or-noise into a locatable bid: declared scope, beneficiaries, dependencies, and provenance, positioned against what
already exists.

The model does not make the threat disappear; an undeniably better candidate threatens the incumbent whether or not it is in a graph.
Because creation is cheap and recognition is hard to deny, the incumbent's optimal defense shifts from winning the comparison to preventing the
comparison from ever becoming structural - keeping the artifact from being built, or from being seen.
The model is therefore necessary but not sufficient.
It makes structural comparison possible; only a polity that requires allocation decisions to be made against the model makes it happen.
Where no such requirement exists, a single authority can keep a two-hour analysis illegible indefinitely simply by declining to look.

The federation property is what makes this affordable at scale.
Traditional standards bodies are expensive - committees, RFCs, vendor wrangling, years to adoption.
The product management model lets any author publish a formal authoritative definition cheaply, lets others reference or compete with it on equal structural terms, and lets consumers reason about the resulting graph without committing to any one definition being canonical.
*While the grass grows, the horse starves*: a cheap standard that addresses a real need within a defined scope is more useful than an expensive standard that arrives years late and still does not fit.
Many cheap standards in a federated graph, each clearly bounded, are more useful still.

The pattern is not new at the code level. Maven Central, npm, PyPI, and the GitHub ecosystem are exactly this: federated graphs of cheap, competing, individually-authoritative libraries that each address some problem in some way, with consumers picking what fits. The registries themselves are fungible - Maven Central is one public hub, but corporations proxy it through Artifactory or Nexus, and any author can publish their own snapshot repository as a static web site reachable by ordinary Maven coordinates; the registry layer is itself a non-differentiating capability with many providers.

Want to parse HTML in Java? You have a dozen options - Jsoup, TagSoup, HtmlCleaner, NekoHTML, JTidy, parser combinators, and more - each with different trade-offs around performance, conformance, malformed-input tolerance, and license; no committee chose; the ecosystem sorts itself by use. The same shape governs JSON parsing: Jackson, Gson, JSON-B, Moshi, and the deliberately-minimal json.org reference implementation for the developer who chooses fewer dependencies over more features. The choice between them is often about *breakage surface* as much as about features. The heavier the framework, the larger the area in which something can break - frequently in a sub-system the consumer never touched - when a transitive dependency conflicts. A cheap minimal library that does one thing carries a smaller breakage surface than a comprehensive framework that does many things; sometimes the small option is the correct one for that reason alone, even when the larger option's feature set looks superior on paper. The same shape governs date formatting, HTTP clients, image manipulation, logging, and most other commodity capabilities at the library level.

The product management model shifts the same pattern *left* - from code dependencies to strategic ones.
Personas, concerns, capabilities, and the relationships among them become first-class artifacts on the same kind of federated graph, published the same way, federated the same way, citable the same way, with the same affordability property that made the code-level ecosystem work in the first place.
The strategic-layer artifacts inherit the same registry fungibility: a product management model can be published to Maven Central, to a corporate Artifactory, to a private GitHub repository, or to a static web site, and consumers reach it through ordinary Maven coordinates regardless of where it lives. The hosting decision is independent of the artifact's structure.
Shift-left is the engineering vocabulary for moving concerns earlier in the lifecycle; the product management model is what it looks like when that movement reaches the strategy layer.

## Design

The metamodel reflects a set of design choices that, together, distinguish it from roadmap tools, EA modeling tools, and idea-management systems. 
Each choice has consequences; this section makes them explicit.

### Federated by construction

The model is authored as text in version control and published as Maven artifacts.
Multiple authors, teams, and organizations publish their own models and reference each other's published models the same way code references its dependencies.
There is no central server, no shared database, no administrative tool that has the authority to edit anyone else's model.
A consumer's view is whichever set of artifacts they choose to load.

Federation is in the artifacts themselves rather than in a service.
This makes the model survive vendor change, organizational reorganization, and tooling churn: a Maven artifact published today remains loadable in a decade by any consumer who has it.

### Why this is federation and not a central registry

The usual answer to "we cannot find or compare our capabilities" is to stand up a central capability registry: one tool, one database, one team that owns the catalog. It is worth being explicit about why this model deliberately does not do that, because the central registry is the option most organizations reach for first and it has a predictable way of killing the very effort it was meant to help.

A central registry separates a capability's description from the capability. The metadata lives in the registry tool; the capability lives in a repository. Now there are two artifacts to keep in sync, and the registry entry is updated by hand, late, or never. The team that owns the capability experiences the registry as overhead that delivers them nothing: they already know what they built, and maintaining a second description of it in someone else's tool is pure tax. Worse, the registry tool has its own data model, and a team whose capability does not fit that model has to either distort the capability to fit the tool or keep the real description elsewhere and copy a lossy summary into the registry. Either way the authoritative description and the registered description drift apart, and the registry's contents decay into a directory of stale stubs.

A central registry also has no good place for work in flight. Capabilities are developed on branches, in forks, in proposals that may never merge. A registry that records only the released, blessed state cannot represent an in-development capability without becoming a workflow tool itself, and a registry that tries to track every branch becomes a second version control system competing with the real one. The model avoids the dilemma by keeping the description in the same repository as the work, on the same branch, versioned by the same commits. An in-flight capability is described in the branch where it is being built; it federates into a consumer's view exactly when that consumer chooses to load that branch's artifact, and not before. Lifecycle state (Proposed, Elaborated, In Development, Available) is carried in the model rather than implied by whether something made it into the registry.

The deeper failure is cultural. A central registry implies a central owner, and a central owner imposes a single schema, a single review process, and a single notion of what a capability is allowed to look like. A team handed that uniformity loses the ability to describe their capability in the terms that made it valuable; their work is fitted to a common shape that serves the catalog's consistency rather than the capability's fidelity. Federation inverts this: each team's description is authoritative for their capability and travels with their code, other teams reference it as a dependency, and a consumer's catalog is whatever set of artifacts they assemble. There is consistency where consistency earns its keep (shared persona and concern vocabulary, the metamodel itself) and local freedom everywhere else. The catalog is a function of what is published and loaded, not a gate every capability must pass through to become visible. Discoverability and comparison, the things the central registry promised, are delivered by the shared metamodel and the federated graph, without the central tool that taxes producers and decays into staleness.

### Identity by URI, rendering by path

Every model element has a global identity expressed as a URI.
Containment - the path from the model root to the element - is a way of rendering identity, not a definition of it.
Two elements with the same URI are the same element regardless of where they appear in any artifact.
References, links, and external citations are always by URI; paths are computed for display.

This separation lets the same element be authored in one place, cited from many, and rendered in whichever location best fits the consumer's view.
It also makes permalinks stable across reorganizations: when an element moves in the rendered tree, its URI does not change.

### Pull-up resolution

When the same element is encountered through multiple containment paths in a loaded view, its canonical home is the most-specific common ancestor of those paths.
A concern written inline inside one capability stays there.
The same concern, authored inline in five capabilities with the same URI, is promoted to the smallest enclosing scope that contains all five - typically the parent capability domain or product model.

The mechanism enables emergent structure: authors write concerns wherever they notice them, and the resolver promotes shared concerns automatically when sharing becomes evident.
Authors do not have to predict in advance which concerns will be shared.
The model becomes cleaner over time without anyone refactoring it, and cross-cutting concerns surface as a structural property of the federation rather than as the result of a manual audit.

Promotion respects authoring intent: elements merge only on identical URIs, never on textual similarity.
Apparent duplicates with different URIs remain separate and are surfaced as candidate equivalences for the author to confirm.
Where promotion would change behavior an author wishes to prevent, an explicit scope declaration keeps the element local.

### Three views of the same elements

The model serves three intersecting views, each useful to a different audience:

- **Polity-allocation view.** Who has authority to bind decisions about resource allocation toward addressing persona concerns.
- **Exchange view.** Concerns are asks; capabilities are bids; work packages are bids on concerns.
- **Decision-binding view.** The incremental binding of decisions over time, with provenance and authority attached to each binding.

The same model elements participate in all three views. No view requires its own separate model.

### Provenance and authority on every assertion

Every assertion in the model - that a persona has a concern, that a capability addresses a concern, that a provider commits to deliver, that a capability is Approved or Withdrawn - carries the authority making the assertion, the period during which the assertion holds, and references to supporting evidence. Signed Maven publication inherits the provenance of artifact signing.

The consequence is that disagreement is legible rather than suppressed.
Two authorities asserting different things about the same capability produce two parallel assertions, both attributable.
The model does not pick a winner; the consumer chooses which authorities to trust for which kinds of assertions.
The asymmetric-blame pattern - where a high-status promise slips and downstream teams absorb the cost - is interrupted because the original promise remains a dated, attributable, durable record.

### Roles as generalized RACI

Authority over a persona, concern, or capability is itself modeled.
Roles are not fixed to a predefined set such as Responsible, Accountable, Consulted, Informed; a model defines whatever roles its domain needs - Owner, Reviewer, Approver, Sponsor, Steward, Auditor, Dissenter - and assigns actors to them for stated intervals.
RACI and its variants are expressible as conventions within this mechanism rather than baked into it.

### Participants: personas that provide capabilities (designed refinement)

The pipeline reads left to right - persona → concern → capability → capability provider - but many of the interesting elements sit on both ends at once. A tax preparer or a mortgage agent provides capabilities to other personas and simultaneously holds concerns of their own that other capabilities address. An AI agent is the same shape: it provides a service to the persona it acts for, and it consumes tool capabilities to do so. The [persons and teams as capability providers](#persons-and-teams-as-capability-providers) use case already treats people as providers; what it does not yet state is that those same people are personas, and that the two facets belong to one element.

Making Persona extend CapabilityProvider (or the reverse) would force the union onto every persona and every provider, which is wrong in both directions: a customer persona provides nothing, and a software product holds no concerns. The designed refinement is instead a **Participant**: a class that extends both Persona and CapabilityProvider, for the elements that genuinely are both. The name comes from the model's own [exchange view](#three-views-of-the-same-elements) - concerns are asks, capabilities are bids, and an element that both asks and bids is a participant in the exchange. A Participant's provided capabilities are pulled by other personas' concerns; its own concerns pull capabilities from elsewhere on the graph; and the chain *customer concern → mortgage agent's provided capability → mortgage agent's own concerns → tools and capabilities the agent consumes* becomes a traversal rather than two disconnected models of the same person.

This is a designed refinement rather than a shipped class: like the execution-layer components, it is stated here so it can be cited, disputed, and pulled into the metamodel when a consuming model needs it.

### Relative and absolute temporals

A temporal value may be absolute (a calendar instant) or relative to another temporal (an offset from an anchor event).
"Fourteen days after security review approval" is a first-class expression, not a comment.
When a relative temporal's anchor acquires a concrete instant - an approval is granted, a release ships - every relative temporal that depends on it resolves automatically.

Template models with deferred binding fall out of this directly: a product launch playbook, a regulatory approval sequence, or a vendor onboarding plan is authored once in relative temporals and instantiated against concrete contexts without modification. 
Cross-model relative temporals reach across federation boundaries, so a consuming team's commitments expressed relative to a platform team's published release resolve when the platform team publishes the date.

### Capability as a self-contained domain

A capability is not only an element addressing concerns; it is also a domain.
It may contain its own personas (Compliance Officer, FinOps Lead, Model Steward - personas surfaced by virtue of the capability existing),
its own concerns (intrinsic properties such as deterministic behavior under fixed inputs, or retention policies that exist by virtue of the capability being subject to them), and its own evidence supporting both.

A capability published as a Maven artifact is therefore self-contained: a consumer can load just that artifact and have a complete local picture of the capability without needing to load the entire enterprise model.
Other capabilities can reference its introduced personas and concerns, so the vocabulary one capability creates becomes reusable across the federation.

The distinction between addressed concerns and introduced concerns is important.
Capabilities are concern generators as well as concern addressers.
The capability that introduces a concern is often not the same as the capability that addresses it - a Cost Attribution capability may address concerns introduced by AI Assistance.
The model carries both relations as first-class structure.

### Capability composition and dependency

Capabilities decompose into sub-capabilities, expressing the part-of hierarchy familiar from TOGAF and ArchiMate capability maps.
Capabilities also declare contract-level dependencies on other capabilities - prerequisite, enhancing, or conflicting - distinct from the implementation-level requirements declared by specific providers.
A fraud-detection capability may declare a prerequisite on transaction-processing at the contract level;
a specific fraud-detection provider may further require a particular streaming infrastructure at the implementation level.
Contract dependencies travel with the capability; implementation requirements travel with the provider.

### Domain merging and anchors

Domains - for personas, concerns, capabilities, capability providers, actors - merge across artifacts by shared identity.
Two capability domain fragments with the same identity, published from different Maven artifacts, are understood as two contributions to the same logical domain.
Their contents union into a single merged view at load time.
The merge is computed, not stored; each fragment remains independently owned by its publishing artifact.

For finer-grained composition, domains may publish anchors - named slots within the domain that contributors can target without owning the domain itself.
The pattern mirrors Eclipse Help's link/anchor mechanism and P2's category IDs, applied to product strategy.

### Evidence and lifecycle as first-class structure

Concerns reference evidence with source, excerpt, and confidence, so a claim about a persona's need is grounded rather than asserted.
Capabilities pass through lifecycle states - Proposed, Elaborated, Approved, In Development, Available, Deprecated, Retired, Withdrawn, Superseded - and lifecycle is itself asserted with authority, period, and rationale.
Multiple authorities may assert different lifecycle states for the same capability; the disagreement is legible rather than suppressed.

The same model that documents production capabilities documents the ones still being elaborated.
Withdrawn and Superseded capabilities remain searchable, so prior elaboration is reusable rather than repeatedly rediscovered.
The handoff from idea to implementation is continuous: the elaboration is not thrown away and redone in a delivery tool.

### AI as a first-class consumer

The structure of the model is what makes AI reasoning over product strategy useful.
An AI agent traverses a typed graph with explicit relationships rather than ranking passages of free-form prose.
Answers cite specific elements at specific versions; generated artifacts respect declared prerequisites and conflicts; validation checks coverage, gaps, duplication, and lifecycle consistency over the full federation.
The same substrate that supports human reasoning supports machine reasoning, with the same citations.

The chat-over-capabilities pattern is the user-visible expression of this: a consumer asks how to solve their problem and the agent surfaces capabilities from the loaded assembly, with provenance and version coordinates.
The agent is grounded in the federated capability set, not in the open internet, and is honest about what is missing from the set as well as what is present.

The typed-graph structure also addresses the scale concern that arises with large federated models.
An agent does not need to load the entire model to reason over it.
The graph supports deterministic traversal - capabilities addressing a particular persona's concerns, prerequisites of a proposed capability, the lifecycle history of a domain - so retrieval is precise rather than approximate.
For finer scoping, the [Nasdanika Semantic Mapping Language](https://github.com/Nasdanika-Models/nasdanika-semantic-mapping-language) (NSML) transforms regions of the model into [semantic context models](https://medium.com/nasdanika/why-we-need-a-semantic-context-model-ae231d8004e3) - focused, agent-shaped views derived from the larger federation through declarative rules.
NSML is to Ecore what XSLT is to XML; semantic context models are one of its primary applications in the product-management context.
The agent operates on a context tailored to its task rather than on the whole federation.

NSML itself is an Elaborated capability in the Nasdanika roadmap: designed in detail, not yet built.
It is published as a lot on a street in a new neighborhood - the architecture is staked out, and construction begins when the first consumer signs a contract for it. 
This is the pull lifecycle the metamodel supports: capabilities can be visible, elaborated, and citable before they exist as code, so demand and supply meet on the same artifact.

### Inspecting the model with rules

A model that machines reason over is a model that machines can also check. [Nasdanika Rules](https://rules.models.nasdanika.org/) is a framework for inspecting data for compliance with rules, standards, and guidelines, and it already inspects YAML configuration, Java sources, GitLab repositories, PDF, and Excel. Its concepts line up with what a federated capability model needs. A *Rule* states an expected condition. A *Rule Set* collects rules and rule sets inherit, so an organization-wide rule set can be specialized by a team or, in the agentic case, by a single agent. An *Inspector* enforces rules against a target and reports a *Violation* at a declared *Severity*. A *Waiver* suppresses a violation, permanently for a false positive or temporarily as a grace period, which matters because a real catalog always carries deliberate exceptions that should be recorded rather than silently tolerated. An *Action* attached to a rule or violation describes the remediation, and some actions execute: rename a tool, add a namespace prefix, open a merge request, file an issue and assign it to the owner.

This is the natural home for the quality inspector that a tool catalog needs. Because a tool catalog loads as a model, an inspector can carry rules that no single-description linter expresses, since they are statements about pairs and neighborhoods rather than about one tool in isolation: no two tools in a neighborhood may exceed a similarity threshold without a waiver; every tool must state its purpose and the boundary of what it does not do; a tool's name must be unique within its namespace and must not shadow a tool in another neighborhood; a newly connected server's tools must be placed into a neighborhood before an agent is allowed to see them. The same inspector framework checks the capability model itself for the gaps the [AI agents reasoning over product strategy](#ai-agents-reasoning-over-product-strategy) use case describes: capabilities addressing no concern, personas with unaddressed goals, duplicated capabilities, lifecycle assertions that conflict.

The distinction from the surrounding landscape is *where in the lifecycle the check runs*. Runtime retrieval and semantic routing decide what to surface for a query, and they are improving quickly; gateways and registries decide what an agent is permitted to call and record an audit trail. Rule-based inspection runs earlier, as a build-time or merge-time gate, the way a linter or a dependency check runs in a pipeline. It stops an ambiguous or under-described tool from entering the catalog in the first place, with a severity that can fail the build and an action that can propose the fix. The two layers reinforce each other: a clean, neighborhood-organized catalog is exactly the input on which retrieval and routing perform best. Inspecting a tool catalog is, in this framing, not a new product but the existing rules framework pointed at a new kind of model, which is the same move the metamodel makes everywhere else.

## Agentic execution - designed, not yet built

Everything above describes the substrate: a federated, version-controlled, AI-readable model of personas, concerns, capabilities, providers, evidence, lifecycle, and authority. The substrate is useful in its own right - the "Try the metamodel without installing anything" path below shows that even without dedicated tooling, the structure improves human and LLM-assisted reasoning over product strategy.

The next layer is execution: turning a persona-pull query ("I have this concern; what is available, by whom, at what maturity, under what evidence?") into an assembled response with traceable provenance, and beyond that into agentic workflows that act on the model. Three Nasdanika components are designed for this layer. None of them are yet implemented; all of them are documented, scoped, and citable as Elaborated capabilities in the Nasdanika roadmap.

### NSML - Nasdanika Semantic Mapping Language

[NSML](https://github.com/Nasdanika-Models/nasdanika-semantic-mapping-language) declaratively transforms regions of the federated model into [semantic context models](https://medium.com/nasdanika/why-we-need-a-semantic-context-model-ae231d8004e3) - focused, agent-shaped views derived from the full model through mapping rules. NSML is to Ecore what XSLT is to XML: a transformation language whose source is a typed graph and whose target is another typed graph.

In the product-management context, NSML is the mechanism by which a large federated model is reduced to the slice an agent needs for a particular task. A pull query for "capabilities addressing the FinOps Lead persona's cost-attribution concerns, with available evidence, at Available or In Development maturity" becomes an NSML transformation that materializes precisely that slice. The agent reasons over the slice; the rest of the federation is not loaded.

### Waypoint - flow orchestration

[Waypoint](https://github.com/Nasdanika/waypoint) is a flow-based orchestration framework. In agentic terms, a Waypoint flow composes capability invocations, tool calls, model traversals, and LLM steps into a directed graph whose execution is observable and resumable.

For the product management model, Waypoint flows carry persona-pull queries from articulation to assembled response. A query enters a flow; an NSML transformation produces the relevant slice; an LLM step assembles a candidate response over the slice; downstream steps attach citations, validate against lifecycle and authority constraints, and produce a verifiable artifact. The flow itself is a first-class artifact, citable from the model, so the question "how did this answer come to be?" has a structural answer rather than a transcript.

### OpGraph - generic execution graph

[OpGraph](https://op-graph.models.nasdanika.org/) is a generic execution model: every operation is a node in a typed graph whose edges carry inputs, outputs, dependencies, and provenance. OpGraph is the substrate on which Waypoint flows execute, and on which any other operational pipeline expressible as a typed graph can run.

For agentic execution, OpGraph means that an agent's work product is itself modeled - which capabilities it consulted, which providers it queried, which evidence it cited, which conclusions it drew, under which authority. The agent's output is auditable in the same shape as the product management model itself; the substrate that documents the product is the substrate that records how decisions were assembled about it.

### Privacy and agent-backed operations

Two NSML capabilities make this layer viable where openness is not acceptable and where consultation has to be more than a sidebar.

**Access control through transformation** assembles the federation in a protected environment and produces audience-scoped views through NSML transformations whose policies are themselves auditable artifacts, expressible in terms of [Apache Shiro](https://shiro.models.nasdanika.org/) primitives (subjects, roles, groups, URI-based permissions). An agent reasoning over a produced view sees only what the view exposes; the user's confidentiality boundary is the agent's confidentiality boundary by construction rather than by trust.

**Agent-backed operations** declared on a view turn interactive consultation into a first-class part of the consumer's contract with the model. Operations like "summarize this for my role" or "find capabilities like this for a different persona" are invocations on the view with typed signatures, citable provenance, and access bounds inherited from the view. Conversations stop being a parallel channel and become part of the structure; the model is the interaction surface, not an artifact about which interaction happens separately.

### Cost, ROI, and pull-driven prioritization

The components above are at the Elaborated lifecycle state. Their architectures are documented, their interactions are designed, and the implementation work is bounded.

In the era of LLM-assisted development and coding agents, the implementation cost for this layer is on the order of a few hundred focused engineering hours. That is small in absolute terms, and very small relative to the productivity gain the product management model itself unlocks even without the execution layer. The ROI is asymmetric: the substrate is already useful as a reasoning environment for humans and LLMs; the execution layer turns that environment into first-class infrastructure that workflows can be built on. Adoption of the substrate is what justifies completing the layer above it.

The investment is consistent with the pull lifecycle the metamodel supports. NSML, Waypoint, and OpGraph are visible, elaborated, and citable before they exist as code. When the first consumer signs a contract for them - a real persona-pull workload in a real organization that justifies turning the design into a runtime - construction begins on the same artifact the design was published on. Demand and supply meet on the model that describes them, and the implementation effort is sized to be repaid in weeks rather than years of operation.
