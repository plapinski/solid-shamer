# Design Patterns — Code-Level Application Reference

GoF design patterns scoped to code-level application — when a pattern is the right fix for a detected smell or principle violation.

---

## Creational Patterns

### Factory Method
**Intent:** Define an interface for creating objects, letting subclasses decide which class to instantiate.
**Problem It Solves:** Object creation logic is complex or depends on runtime conditions. `new` calls are scattered with conditional logic.
**When to Apply:** Creator (GRASP) violations — object creation doesn't belong in the current class. Type code determines which class to instantiate.
**Key Tradeoff:** Introduces a class hierarchy for creation. Overkill if creation logic is simple or unlikely to vary.
**Smell Fix:** Removes conditional `new` calls, supports OCP when new types are added.

### Abstract Factory
**Intent:** Provide an interface for creating families of related objects without specifying concrete classes.
**Problem It Solves:** System must create families of related products. Mixing product families causes inconsistency.
**When to Apply:** Multiple Factory Methods that always vary together. Need to ensure related objects are compatible.
**Key Tradeoff:** Significant complexity. Only justified when product families are a real requirement, not a hypothetical future need (YAGNI).
**Smell Fix:** Eliminates parallel conditional logic for creating related objects.

### Builder
**Intent:** Separate the construction of a complex object from its representation.
**Problem It Solves:** Constructor with many parameters. Complex multi-step object creation.
**When to Apply:** Long Parameter List smell on constructors. Object creation requires >4 parameters or conditional construction steps.
**Key Tradeoff:** Adds a builder class. Fluent API is pleasant but adds indirection. Consider simple DTOs or named constructors first.
**Smell Fix:** Resolves Long Parameter List on constructors, clarifies complex creation sequences.

### Singleton
**Intent:** Ensure a class has exactly one instance and provide a global access point.
**Problem It Solves:** Resource that should exist exactly once (database connection pool, configuration).
**When to Apply:** Rarely needed — modern DI containers handle single-instance registration. Use container-managed singleton bindings instead.
**Key Tradeoff:** Global state, hard to test, hidden dependency. Almost always wrong in application code — use container-managed instances instead.
**Smell Fix:** N/A — Singleton is itself often a smell in modern applications.

### Prototype
**Intent:** Create new objects by cloning an existing instance rather than constructing from scratch.
**Problem It Solves:** Object creation is expensive (complex setup, external resource loading) or construction logic shouldn't be exposed to the client.
**When to Apply:** When objects differ only slightly from a base configuration. When construction involves costly operations (database lookups, file parsing) that can be done once.
**Key Tradeoff:** Requires implementing a proper clone/copy mechanism. Deep vs. shallow copy must be carefully considered for objects with references.
**Smell Fix:** Reduces complex constructor logic, supports Creator (GRASP) by localizing object creation knowledge.

---

## Structural Patterns

### Adapter
**Intent:** Convert the interface of a class into another interface clients expect.
**Problem It Solves:** Incompatible interfaces between existing classes. Third-party library doesn't match your domain interface.
**When to Apply:** Protected Variations (GRASP) — wrapping a volatile external dependency. Incomplete Library Class smell.
**Key Tradeoff:** One extra class per adapted interface. Worth it for third-party boundaries; overkill for internal code you control.
**Smell Fix:** Resolves Incomplete Library Class, supports DIP by abstracting external dependencies.

### Decorator
**Intent:** Attach additional responsibilities to an object dynamically.
**Problem It Solves:** Need to add behavior without modifying existing class. Multiple optional behaviors that combine in various ways.
**When to Apply:** Cross-cutting concerns (logging, caching, validation) on existing services. OCP — extend behavior without modification.
**Key Tradeoff:** Many small classes. Can be hard to debug through layers. Framework middleware is the Decorator pattern applied.
**Smell Fix:** Avoids modifying existing classes (OCP), replaces conditional behavior additions.

### Composite
**Intent:** Compose objects into tree structures. Treat individual objects and compositions uniformly.
**Problem It Solves:** Part-whole hierarchies where clients need to treat parts and wholes the same way.
**When to Apply:** Menu systems, organizational hierarchies, nested category structures, permission trees.
**Key Tradeoff:** Makes the design overly general if the hierarchy isn't natural. Can make it hard to restrict what the tree contains.
**Smell Fix:** Eliminates type-checking between leaf and composite objects.

### Facade
**Intent:** Provide a unified interface to a set of interfaces in a subsystem.
**Problem It Solves:** Client code depends on many subsystem classes. Complex internal interactions exposed to callers.
**When to Apply:** Simplify a complex subsystem for outside consumers. Reduce coupling to internal implementation details.
**Key Tradeoff:** Can become a God Class if it grows unchecked. Should delegate, not accumulate logic.
**Smell Fix:** Reduces coupling (Message Chains, Inappropriate Intimacy with subsystem internals).

### Proxy
**Intent:** Provide a surrogate or placeholder for another object to control access to it.
**Problem It Solves:** Lazy loading, access control, logging, or caching around an expensive object.
**When to Apply:** ORM relationships often use lazy-loading proxies. Useful for wrapping external service calls with caching.
**Key Tradeoff:** Extra indirection. Must faithfully mirror the real subject's interface.
**Smell Fix:** Supports SoC by separating cross-cutting concerns from core logic.

### Bridge
**Intent:** Decouple an abstraction from its implementation so the two can vary independently.
**Problem It Solves:** Proliferating class combinations when two dimensions vary independently (e.g., shapes x renderers, notifications x channels).
**When to Apply:** Parallel Inheritance Hierarchies smell — every time you add a variant on one axis, you need corresponding classes on another axis.
**Key Tradeoff:** Adds indirection between abstraction and implementation. Justified when both dimensions genuinely vary; overkill for a single-axis hierarchy.
**Smell Fix:** Eliminates Parallel Inheritance Hierarchies, reduces class explosion from combinatorial growth.

### Flyweight
**Intent:** Share fine-grained objects efficiently to reduce memory usage.
**Problem It Solves:** Large numbers of similar objects consume excessive memory. Each instance carries duplicated intrinsic state.
**When to Apply:** When many objects share the same intrinsic state (e.g., character formatting in a text editor, tile types in a game map, shared configuration across thousands of instances).
**Key Tradeoff:** Separates intrinsic (shared) from extrinsic (context-dependent) state. Adds complexity to manage the shared pool. Only valuable when object count is genuinely large.
**Smell Fix:** Reduces memory overhead from Primitive Obsession at scale, supports High Cohesion by separating shared from contextual data.

---

## Behavioral Patterns

### Strategy
**Intent:** Define a family of algorithms, encapsulate each one, and make them interchangeable.
**Problem It Solves:** Switch Statements smell — algorithm selection based on type or configuration.
**When to Apply:** Conditional logic choosing between algorithms. Notification channels, payment methods, export formats, pricing rules.
**Key Tradeoff:** One class per strategy. Worth it when there are 3+ variants or variants will grow. For 2 simple cases, a conditional may be clearer (KISS).
**Smell Fix:** Switch Statements, conditional behavior selection. Enables OCP — new strategies without modifying existing code.

### Observer
**Intent:** Define a one-to-many dependency so that when one object changes state, all dependents are notified.
**Problem It Solves:** Tight coupling between an object and its dependents. Change notification scattered through code.
**When to Apply:** Event-driven reactions. Framework event/listener systems are the Observer pattern.
**Key Tradeoff:** Indirection makes it harder to trace execution flow. Can cause unexpected cascading effects.
**Smell Fix:** Shotgun Surgery (change notifications), Divergent Change (notifying dependents from the source class).

### Command
**Intent:** Encapsulate a request as an object, allowing parameterization, queuing, and undo.
**Problem It Solves:** Need to decouple the invoker of an action from the object that performs it. Need to queue, log, or undo operations.
**When to Apply:** Queued jobs and commands use this pattern. Action classes are a lightweight variant.
**Key Tradeoff:** One class per command. Justified for queued/undoable/auditable operations. For simple synchronous calls, may be overkill.
**Smell Fix:** Fat Controller (extract action into a command), supports SRP for the invoking class.

### Template Method
**Intent:** Define the skeleton of an algorithm in a base class, letting subclasses override specific steps.
**Problem It Solves:** Similar algorithms in multiple classes with the same structure but different details.
**When to Apply:** Parallel inheritance with similar algorithms. Form Template Method refactoring.
**Key Tradeoff:** Uses inheritance — inflexible if you need to mix steps from different templates. Consider Strategy for more flexibility.
**Smell Fix:** Duplicate Code in sibling subclasses with structural similarity.

### State
**Intent:** Allow an object to alter its behavior when its internal state changes.
**Problem It Solves:** Complex conditional logic based on object state. State transitions scattered through methods.
**When to Apply:** Objects with defined states and transitions (orders, subscriptions, workflows). When state-dependent behavior is duplicated across methods.
**Key Tradeoff:** One class per state. Worth it when states are complex and growing. For simple 2-3 state enums with minimal behavior, may be overengineering.
**Smell Fix:** Switch Statements on state fields, conditional logic duplicated across methods by state.

### Chain of Responsibility
**Intent:** Pass a request along a chain of handlers, each deciding to process or pass it on.
**Problem It Solves:** Multiple objects might handle a request, and the handler isn't known in advance.
**When to Apply:** Framework middleware is this pattern. Validation chains, approval workflows, request processing pipelines.
**Key Tradeoff:** No guarantee of handling. Can be hard to debug which handler processed the request.
**Smell Fix:** Complex conditional routing logic, replacing if-else chains for request handling.

### Iterator
**Intent:** Provide a way to access elements of an aggregate sequentially without exposing its underlying representation.
**Problem It Solves:** Need to traverse a collection without depending on its internal structure.
**When to Apply:** Most languages provide iterator interfaces and collection libraries. Rarely need to implement custom iterators — use existing framework support.
**Key Tradeoff:** Minimal since iteration is built into most languages and frameworks. Custom iterators add complexity only when traversal logic is genuinely complex.
**Smell Fix:** Inappropriate Intimacy with collection internals, Encapsulate Collection refactoring.

### Specification
**Intent:** Combine business rules using boolean logic to filter, validate, or select objects.
**Problem It Solves:** Complex query/filter conditions duplicated or embedded in repositories.
**When to Apply:** When the same filtering logic appears in multiple queries. When business rules for selection are complex and combinable.
**Key Tradeoff:** Extra classes for rules. ORM query scopes or builder patterns serve a similar purpose for simpler cases.
**Smell Fix:** Duplicate Code in query conditions, complex conditionals in repositories.

### Mediator
**Intent:** Define an object that encapsulates how a set of objects interact, promoting loose coupling.
**Problem It Solves:** Many-to-many communication between objects creates a tangled web of dependencies. Each object references many others.
**When to Apply:** Inappropriate Intimacy between multiple collaborators. When adding a new participant requires modifying all existing participants.
**Key Tradeoff:** Centralizes control, which can create a God Class if the mediator grows unchecked. Best for coordinating interactions, not accumulating business logic.
**Smell Fix:** Inappropriate Intimacy between multiple collaborators, reduces coupling in complex interaction networks.

### Memento
**Intent:** Capture and externalize an object's internal state so it can be restored later, without violating encapsulation.
**Problem It Solves:** Need to implement undo/redo, history, or snapshots without exposing object internals.
**When to Apply:** Undo/redo functionality, version history, save points in workflows, transaction rollback.
**Key Tradeoff:** Memory usage from storing snapshots. Must manage memento lifecycle (how many to keep, when to discard). Deep state may require deep copying.
**Smell Fix:** Supports encapsulation — avoids exposing internal state via getters just to save/restore it.

### Visitor
**Intent:** Represent an operation to be performed on elements of an object structure, allowing new operations without modifying the elements.
**Problem It Solves:** Need to perform multiple unrelated operations on a stable class hierarchy without polluting those classes with operation-specific code.
**When to Apply:** When the class structure is stable but operations on it change frequently (e.g., AST processing, document export in multiple formats, report generation from a data model).
**Key Tradeoff:** Adding new element types is hard (every visitor must be updated). Best when the element hierarchy is stable but operations vary. Inverts the usual OCP axis.
**Smell Fix:** Prevents Divergent Change in element classes, avoids Feature Envy from external operation classes reaching into element internals.

### Interpreter
**Intent:** Define a representation for a language's grammar and an interpreter that uses the representation to interpret sentences.
**Problem It Solves:** Recurring problem that can be expressed as sentences in a simple language. Need to evaluate rules, expressions, or DSLs.
**When to Apply:** Rule engines, query builders, mathematical expression evaluators, configuration DSLs, validation rule parsing.
**Key Tradeoff:** Only practical for simple grammars. Complex languages need parser generators instead. Can be slow for large inputs compared to compiled approaches.
**Smell Fix:** Replaces complex conditional logic for rule evaluation, supports OCP when new grammar rules are added as new expression classes.
