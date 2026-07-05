A capability extends PersonaDomain, ConcernDomain, and EvidenceDomain to support self-contained capability definitions.
A capability may:

- Address concerns owned by external personas or other capabilities (via AddressedConcerns)
- Introduce capability-specific personas (via PersonaDomain)
- Introduce concerns that exist by virtue of the capability itself, not owned by any persona (via ConcernDomain)
- Carry the evidence supporting all of the above (via EvidenceDomain)
- Decompose into sub-capabilities (via subCapabilities)

## Capability language

> "Language shapes the way we think, and determines what we can think about." - Benjamin Lee Whorf

Capabilities in the model carry grammatical roles when they compose.
A capability like _document signing_ is verb-shaped - it does something.
A capability like _audit logging_ is adjective-shaped — it qualifies how something is done.
The model represents both as first-class capabilities, and the composition between them (audit _modifying_ signing) is a structural relationship, not a free-form annotation.

This grammar matters because it determines what compositions are easy to _express_.
A consumer who needs _signed documents with audit and async delivery_ composes three first-class elements - _signing_ (verb), _audit_ (adjective), _async_ (adjective) - rather than writing a paragraph about what they want.
The expression is a noun phrase the model understands; the dependencies that connect the elements carry the syntax.
A federation that supports composition as grammar lets consumers articulate what they need in ways the catalog's authors did not have to anticipate;
a federation that only enumerates capabilities flattens an expressible space into a finite list.

Federation across teams is, in this reading, a shared vocabulary.
Different teams contribute different words.
One team owns _signing_; another owns _audit_; a third owns _async_.
Together, the vocabulary supports expressions no single team would have authored, and the combinations that emerge across federated catalogs are themselves structural insights.
Elicitation by an AI agent becomes structured for the same reason - the agent asks the stakeholder for the verbs they need, the adjectives that qualify them, the sequence in which they combine, and the output is a noun phrase the model can map.

Whorf's observation - that language shapes the way we think, and determines what we can think about - applies to capability vocabularies as much as to natural languages.
The grammar is what makes the federated model useful as a thinking surface, not just as a catalog.

