# CUPID Properties — Reference

CUPID is Daniel Terhorst-North's alternative to SOLID, focusing on properties of "joyful" code. Where SOLID constrains, CUPID describes qualities that make code pleasant to work with.

---

## Composable

**Definition:** Code that plays well with others — easy to use as a building block in contexts its author didn't anticipate.

**How It Differs from SOLID:** SOLID focuses on internal structure; Composable focuses on external usability. A class can satisfy SRP but be hard to compose (tight coupling to environment).

**Evaluation Criteria:**
- Can this class be used outside its current context without modification?
- Does it depend on minimal, well-defined inputs?
- Can its output be consumed by other components easily?
- Is it free from hidden dependencies on global state, environment, or framework magic?

**Anti-Patterns:**
- Method with >4 parameters (suggests it does too much to compose easily)
- Class that requires a specific framework lifecycle to function
- Implicit dependency on request context, session, or global configuration
- Hard dependency on concrete infrastructure (database, filesystem) without abstraction

---

## Unix Philosophy

**Definition:** Code that does one thing well — small, focused, and single-purpose.

**How It Differs from SOLID:** SRP says "one reason to change"; Unix Philosophy says "do one thing and do it excellently." It's about scope and excellence, not change axes.

**Evaluation Criteria:**
- Can you describe what this class/method does in one sentence without "and"?
- Does it excel at its single purpose?
- Is its output useful as input to other components?
- Does it avoid absorbing adjacent responsibilities over time?

**Anti-Patterns:**
- Swiss-army-knife classes that handle many related-but-distinct concerns
- Methods that transform, validate, AND persist in one flow
- "God objects" that accumulate responsibilities because they're convenient
- Services that grow by accretion rather than composition

---

## Predictable

**Definition:** Code that behaves as expected — no surprises, consistent, and deterministic where possible.

**How It Differs from SOLID:** LSP addresses substitution correctness; Predictable addresses cognitive load. Code should do what its name and signature suggest, nothing more.

**Evaluation Criteria:**
- Does the method name accurately describe ALL its effects?
- Are side effects visible in the method signature?
- Does it return consistent types (no mixed return types based on conditions)?
- Are error cases handled explicitly, not silently?

**Anti-Patterns:**
- Method named `getUser()` that also logs, caches, and triggers events
- Returning `null` sometimes and throwing exceptions other times for the same error
- Silent error swallowing with empty catch blocks
- Methods that modify input parameters as a side effect
- Setter methods that trigger business logic beyond simple assignment

---

## Idiomatic

**Definition:** Code that feels natural in its language and framework — follows established conventions and patterns.

**How It Differs from SOLID:** SOLID is language-agnostic; Idiomatic is language-specific. Code should feel like it belongs in its ecosystem.

**Evaluation Criteria:**
- Does it use language features as intended? (e.g., typed properties, enums, pattern matching)
- Does it follow framework conventions?
- Would an experienced developer in this stack recognize the patterns?
- Does it avoid reimplementing what the framework provides?

**Anti-Patterns:**
- Rolling custom authentication instead of using the framework's auth system
- Manual data manipulation instead of using built-in collection/stream utilities
- Writing raw queries where the ORM/query builder works well
- Over-engineered patterns from other languages (excessive interfaces for simple cases)
- Fighting the framework's conventions because of habits from another language

---

## Domain-Based

**Definition:** Code that reflects the problem domain in its structure and language — not just the technical solution.

**How It Differs from SOLID:** SOLID organizes by technical concerns; Domain-Based organizes by business meaning. The code should read like a description of the business process.

**Evaluation Criteria:**
- Do class and method names use domain language (ubiquitous language)?
- Does the code structure reflect business concepts, not just technical layers?
- Can a domain expert understand the intent by reading class/method names?
- Do variable names reflect business entities, not implementation details?

**Anti-Patterns:**
- `DataProcessor`, `ItemHandler`, `ThingManager` — generic technical names
- `arr`, `data`, `items` — instead of `orders`, `invoices`, `customers`
- Methods named `process()`, `handle()`, `execute()` without domain context
- Package/namespace structure that reflects only technical layers (services, repositories) without domain grouping
