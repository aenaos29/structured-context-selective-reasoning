# Structured Context and Selective Reasoning

## A proposal for efficient AI knowledge retrieval and reasoning

```text
Query
  ↓
Attributes / Tags
  ↓
Relevant IDs
  ↓
Known Entry Point or Previous Answer IDs
  ↓
Relationship Traversal
  ↓
Lower-Cost Engine Locates
  ↓
Selected Context
  ↓
Higher-Capability Engine Reasons
  ↓
Validation / Early Stop
```

### Summary

This proposal describes a possible architecture for reducing unnecessary context and reasoning cost in large AI systems by separating:

* information storage,
* information location,
* relationship traversal,
* and high-level reasoning.

The central idea is:

> **Store richly. Locate cheaply. Retrieve selectively. Reason only where necessary.**

This concept emerged while designing a software system intended to operate in multiple editions with different capability levels, including deliberately reduced versions.

The original problem was not how to store less information.

It was how to avoid losing or limiting information simply because one version of the software had fewer capabilities than another.

A reduced version should be able to use only the information it needs or can process, while the underlying knowledge remains complete and reusable by more capable versions.

This led to a broader principle:

> **Reduced capability should not require reduced information.**

---

## Core Representation

A knowledge object could conceptually be represented as:

```text
CATEGORY:ID | ATTRIBUTES
```

For example:

```text
THEORY:1343 | 001 A23 C19
```

Attributes may describe properties such as:

* language,
* domain,
* topic,
* level,
* provenance,
* confidence,
* required tools,
* required model capability,
* reasoning requirement,
* verification requirement.

The serialization format is not the architecture.

The same object could be represented in JSON:

```json
{
  "category": "THEORY",
  "id": "1343",
  "attributes": [
    "001",
    "A23",
    "C19"
  ]
}
```

JSON can remain useful for compatibility while the underlying semantic representation stays compact.

---

## Attributes Reduce the Search Space

Attributes answer:

> **Should this information even be examined for the current request?**

A large knowledge store may contain extensive information that is globally useful but irrelevant to the current question.

Instead of sending large amounts of information to a reasoning model and expecting it to discard what is irrelevant, attributes can narrow the candidate space first.

Conceptually:

```text
LANGUAGE = Greek
DOMAIN = Physics
LEVEL = Secondary
TOPIC = Mechanics
```

Objects outside that neighborhood may never need to enter the active reasoning context.

This allows:

> **Rich stored information without rich active context.**

---

## Tags as a Lightweight Semantic Layer

Attributes are useful for structured filtering, but not every useful property of information needs to become a formal attribute.

A lighter semantic layer can be provided through tags.

For example:

```text
OBJECT: physics:2101

ATTRIBUTES:
LANGUAGE = EN
DOMAIN = PHYSICS
LEVEL = SECONDARY

TAGS:
force
acceleration
mass
motion
mechanics
```

Attributes provide relatively stable structured information.

Tags provide a more flexible description of the semantic neighborhood surrounding an object.

This creates a possible retrieval sequence:

```text
Query
  ↓
Tags identify a semantic neighborhood
  ↓
Attributes reduce the candidate set
  ↓
Relationships provide traversal paths
  ↓
Reasoning operates on the selected information
```

Tags therefore do not replace attributes or relationships.

They serve a different purpose:

> **Tags suggest where relevant information may be located.**

> **Attributes determine which candidates are appropriate.**

> **Relationships determine how those candidates connect.**

---

### Gradual Improvement

The structure could also improve over time without rewriting the underlying information.

If certain tags, relationships, entry points, or traversal paths repeatedly prove useful, the system could record that observation as a candidate improvement.

For example:

```text
frequently successful path
A → PREREQUISITE → B → EXPLAINS → C
```

may later justify:

* strengthening an existing relationship,
* proposing an additional tag,
* creating a more useful entry point,
* or caching a validated traversal path.

Such changes should not automatically become permanent simply because they were used once.

They can instead be treated as:

```text
OBSERVED
→ REPEATED
→ VALIDATED
→ PROMOTED
```

This allows the knowledge structure to improve from use while preserving control over permanent changes.

The objective is not to make the graph continuously rewrite itself.

The objective is to allow successful use to reveal better ways of navigating information.
## Relationships Are Separate

Relationships answer a different question:

> **Where should the system go from here?**

Examples could include:

```text
UNDER
SIMILAR
ABOVE
PREREQUISITE
DERIVED_FROM
SUPPORTS
EXPLAINS
DEPENDS_ON
```

A relation store could conceptually contain:

```text
PREREQUISITE

physics:1343 → physics:2AA2
physics:1343 → math:18F0
```

or:

```json
{
  "physics:1343": [
    "physics:2AA2",
    "math:18F0"
  ]
}
```

This keeps two concerns separate:

> **Attributes describe the object.**

> **Relationships describe how objects connect.**

---

## Known Points of Entry

A system should not necessarily begin every search from zero.

Frequently useful concepts or reliable knowledge landmarks could act as **known points of entry** into the graph.

A new query could follow:

```text
query
→ attribute filtering
→ known point of entry
→ relevant relationships
→ small candidate neighborhood
→ reasoning
```

This reduces unnecessary exploration.

---

## Continuation From Previous Answers

A conversation already contains useful navigation information.

When an answer is produced, the system could retain a lightweight record of the IDs actually used:

```text
USED_FOR_ANSWER

physics:1343
physics:2AA2
math:18F0
```

If the next request continues the same subject, those IDs become the new starting position.

For example:

> "What should I understand before this?"

The system may follow:

```text
PREREQUISITE
```

If the next request is:

> "What knowledge can be derived from this?"

the system may follow:

```text
DERIVED_FROM
```

If the subject changes, the system can return to a known point of entry.

A hybrid approach could continue from previous-answer IDs while independently cross-checking from another entry point.

This could provide conversational continuity without repeatedly reprocessing the entire previous context.

---

## Reasoning Effort as Exploration Budget

Reasoning effort could influence how deeply and broadly the knowledge structure is explored.

Conceptually:

```text
lower effort
→ fewer hops
→ fewer branches
→ less verification

higher effort
→ more hops
→ more branches
→ alternative paths
→ stronger cross-checking
```

This does not need to mean fixed numbers of hops.

Traversal could stop early when sufficient supporting information has already been found.

Effort can therefore control:

* depth,
* breadth,
* alternative paths,
* validation,
* and escalation.

---

## Lower-Cost Engines Locate, Higher-Capability Engines Reason

Not every stage requires the strongest available model.

Lower-cost engines may be sufficient for:

* attribute filtering,
* locating candidate objects,
* classification,
* routing,
* simple semantic matching,
* identifying relevant graph regions.

Their job is not necessarily to solve the final problem.

Their job is to:

> **Reduce the problem.**

A stronger model can then reason over the smaller set of relevant information.

The pipeline becomes:

```text
lower-cost engine
→ locate relevant information

structured retrieval
→ reduce active context

higher-capability engine
→ reason over selected information
```

instead of:

```text
highest-capability engine
→ inspect everything
→ decide what matters
→ reason
```

This may allow expensive reasoning capacity to be concentrated where it adds the most value.

A lightweight engine answers:

> **Where is the relevant information?**

A stronger engine answers:

> **What does this information imply?**

---

## Information Can Describe Its Processing Requirements

Attributes can describe not only what information is, but also how demanding its processing may be.

For example:

```text
MODEL_CAPABILITY = ADVANCED_REASONING
REASONING_REQUIREMENT = HIGH
VERIFICATION = REQUIRED
TOOLS = CALCULATOR
```

These should preferably describe capabilities rather than fixed model names.

For example:

```text
ADVANCED_REASONING
```

is more durable than:

```text
MODEL_X
```

The runtime can map the requirement to whichever models are currently available.

The information itself can therefore help determine the resources required to process it.

---

## Example

Assume a user has just asked about Newton's second law.

The previous answer used:

```text
USED_FOR_ANSWER

physics:2101
physics:2104
math:1042
```

The user then asks:

> "What should I understand before learning this?"

Instead of starting a completely new search, the system can continue from those IDs and follow only:

```text
PREREQUISITE
```

Attributes can further restrict candidates:

```text
DOMAIN = Physics
LEVEL = appropriate
LANGUAGE = user language
```

A lightweight engine may locate:

```text
physics:1802
math:0903
physics:1810
```

Only those objects and the minimal required context are then sent to a stronger reasoning model.

If uncertainty remains, higher reasoning effort may request:

```text
additional hops
alternative prerequisite paths
cross-check from a known mechanics entry point
```

The stronger model therefore spends its reasoning budget on the actual conceptual problem rather than rediscovering the entire information neighborhood.

---

## Expected Advantages

This architecture could potentially support:

* smaller active contexts,
* reduced redundant retrieval,
* better conversational continuity,
* more explicit provenance,
* lower-cost model routing,
* selective use of expensive reasoning,
* shared knowledge across different product capability levels,
* and easier inspection of why particular information was retrieved.

Most importantly:

> **A less capable engine does not require less complete information.**

It can operate on the same knowledge representation while accessing a smaller or simpler portion of it.

---

## Non-Goal

This proposal is not intended to describe or claim knowledge of the internal architecture of any existing AI model.

It is an external knowledge, retrieval, routing, and reasoning-control concept that could potentially complement different model architectures.

It also does not assume that every attribute, tag, or relationship must be manually created.

Automated tagging, learned relationships, confidence scoring, validation, and gradual improvement could be explored separately.

---

## Design Principle

The proposal can be summarized as:

> **Preserve information completely, but expose only what is useful now.**

Attributes narrow the search.

Tags identify semantic neighborhoods.

Relationships define possible paths.

Known points of entry provide fast access.

Previous-answer IDs preserve conversational position.

Lower-cost engines locate information.

Higher-capability engines reason over it.

Reasoning effort controls how far exploration goes.

And the system stops when it has enough evidence to answer.

