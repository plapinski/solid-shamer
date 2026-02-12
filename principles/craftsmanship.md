# Craftsmanship Principles — Reference

---

## Boy Scout Rule

**Definition:** Leave the code cleaner than you found it.

**Violation Indicators:**
- Adding new code that mimics existing bad patterns instead of improving them
- Touching a file without fixing obvious issues in the immediate vicinity
- Copy-pasting flawed code from elsewhere in the codebase

**Recommended Fix:** When modifying a file, improve one small thing in the area you're touching — rename a variable, extract a method, add a type declaration.

---

## DRY (Don't Repeat Yourself)

**Definition:** Every piece of knowledge should have a single, unambiguous, authoritative representation.

**Violation Indicators:**
- Same logic block duplicated in 2+ places (not just similar structure — actually the same business logic)
- Constants defined in multiple places instead of one source of truth
- Repeated query conditions or business rules across methods
- Copy-paste between classes with minimal variation

**Recommended Fix:** Extract shared logic into a method, trait, or class. For repeated values, define constants or configuration.

**Caveat:** Similar-looking code serving different business purposes is NOT a DRY violation. "Duplication is far cheaper than the wrong abstraction." — Sandi Metz

---

## KISS (Keep It Simple, Stupid)

**Definition:** Prefer the simplest solution that meets the requirements.

**Violation Indicators:**
- Design pattern applied where a simple function would suffice
- Abstract base classes for a single concrete implementation
- Metaprogramming or reflection where straightforward code works
- Clever one-liners that require explanation

**Recommended Fix:** Replace with the straightforward approach. Ask "would a junior developer understand this immediately?"

---

## YAGNI (You Aren't Gonna Need It)

**Definition:** Don't build it until you actually need it.

**Violation Indicators:**
- Parameters accepted but never used
- Interfaces with only one implementation and no concrete plan for more
- Configuration options nobody has requested
- Abstract factories for objects created in one place
- Generic solutions to specific problems

**Recommended Fix:** Remove the unused abstraction. Add it when a second use case actually materializes.

---

## Tell, Don't Ask

**Definition:** Tell objects what to do; don't ask for their data and decide for them.

**Violation Indicators:**
- Getting data from an object, making a decision, then telling the object what to do
- `if (order.getStatus() == 'paid') { order.setStatus('shipped') }`
- Chains of getters followed by conditional logic

**Recommended Fix:** Move the decision into the object that owns the data: `order.ship()` — let Order enforce its own rules.

---

## Law of Demeter (Principle of Least Knowledge)

**Definition:** A method should only talk to its immediate collaborators, not reach through them.

**Violation Indicators:**
- Method chains: `this.user.address.city.getZipCode()`
- Reaching into nested objects to access data
- Accessing properties of a returned object's internal state

**Recommended Fix:** Add a delegate method on the immediate collaborator, or restructure so the needed data is passed directly.

---

## Principle of Least Astonishment (POLA)

**Definition:** A component should behave in a way that most users expect; it should not surprise.

**Violation Indicators:**
- Method does more than its name suggests
- Side effects in getters or query methods
- Inconsistent naming within the same module
- Boolean parameters that change method behavior in non-obvious ways

**Recommended Fix:** Rename to match behavior, split into separate methods, or remove surprising side effects.

---

## Command-Query Separation (CQS)

**Definition:** Methods should either change state (command) or return data (query), not both.

**Violation Indicators:**
- Method returns a value AND modifies state
- `result = service.processAndGetResult()` — process is a command, get is a query
- Setter methods that return the previous value

**Recommended Fix:** Split into two methods — one command (void return), one query (no side effects). Exception: fluent interfaces and stack-like operations (`pop()`).

---

## Separation of Concerns (SoC)

**Definition:** Each module should address a single concern — a cohesive set of information or behavior.

**Violation Indicators:**
- Mixing presentation logic with business logic
- Combining data access with domain rules
- Authentication/authorization checks scattered through business logic instead of middleware
- Logging and monitoring interleaved with core logic

**Recommended Fix:** Separate into layers or aspects. Use middleware, decorators, or observers for cross-cutting concerns.

---

## Fail Fast

**Definition:** Report errors as early as possible — at the point of detection, not downstream.

**Violation Indicators:**
- Null checks deep in call chains that could be caught at the entry point
- Methods that silently return default values instead of signaling errors
- Wrapping code in try-catch that silently returns null or a default
- Late validation that allows invalid data to propagate through layers
- Missing guard clauses at method entry

**Recommended Fix:** Add guard clauses at method entry, use type declarations, throw meaningful exceptions early, validate at system boundaries.

---

## Composition over Inheritance

**Definition:** Prefer composing objects from smaller, focused collaborators over building deep inheritance trees.

**Violation Indicators:**
- Inheritance depth >2 levels (beyond framework base classes)
- Subclass that only uses a small portion of parent's behavior
- Inheritance used to share code rather than model "is-a" relationships
- Changes to a base class have unexpected effects on distant subclasses

**Recommended Fix:** Extract shared behavior into composable services, mixins/traits, or strategy objects. Use delegation instead of inheritance.
