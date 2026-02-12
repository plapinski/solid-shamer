# SOLID Principles — Reference

---

## Single Responsibility Principle (SRP)

**Definition:** A class should have only one reason to change — one axis of responsibility.

**Violation Indicators:**
- Constructor with >4 injected dependencies
- Class >300 lines mixing concerns (persistence, validation, notification, formatting)
- Method names containing "And"/"Or" (e.g., `validateAndSave`, `fetchOrCreate`)
- Multiple unrelated public method groups

**Examples:**
- Service class that validates input, calls external APIs, persists data, and sends notifications
- Domain model with business logic in accessors, validation in lifecycle hooks, and query logic all mixed together
- Controller action that does authorization, validation, business logic, and response formatting

**Detection Signals:**
- Ask "what reasons could this class change?" — if >2 unrelated answers, SRP is violated
- Check if the class can be described without "and" — if not, it has multiple responsibilities

**Recommended Refactoring:**
- Extract Class: split into focused classes, one per responsibility
- Extract Method → Extract Class: when methods cluster into groups, extract each group

**Relationship to Other Principles:**
- ISP violations often indicate SRP violations at the interface level
- High Cohesion (GRASP) is the complementary positive formulation

---

## Open-Closed Principle (OCP)

**Definition:** Software entities should be open for extension but closed for modification.

**Violation Indicators:**
- Switch/if-else chains on a type discriminator
- Adding a new variant requires modifying existing classes
- Hardcoded constants or conditions that grow over time
- Methods with conditional blocks like `if (type == 'X') { ... } else if (type == 'Y') { ... }`

**Examples:**
- Notification service with switch on channel type (email, SMS, push) — add strategy pattern
- Report generator with conditionals per report format (PDF, CSV, Excel)
- Payment processor with if-else per payment method

**Recommended Refactoring:**
- Replace Conditional with Polymorphism: introduce a common interface, one implementation per variant
- Strategy Pattern: inject behavior rather than branching on type
- Template Method: extract common algorithm, let subclasses customize steps

**Relationship to Other Principles:**
- Polymorphism (GRASP) is the mechanism for achieving OCP
- LSP ensures substituted implementations behave correctly

---

## Liskov Substitution Principle (LSP)

**Definition:** Objects of a supertype should be replaceable with objects of its subtypes without breaking correctness.

**Violation Indicators:**
- Subclass throws unexpected exceptions
- Override that silently does nothing (no-op)
- Subclass that ignores or contradicts parent behavior
- `instanceof` checks on variables typed to a parent class
- Subclass that strengthens preconditions (requires more than parent) or weakens postconditions (guarantees less)

**Examples:**
- `ReadOnlyRepository extends Repository` that throws on `save()` — violates parent's contract
- Subclass override returning null where parent guarantees a value
- Collection subclass that doesn't maintain iteration order the parent guarantees

**Recommended Refactoring:**
- Extract interface that both classes implement independently (break inheritance)
- Push shared behavior to a common trait or composition
- Redesign hierarchy so subtypes genuinely extend (not restrict) behavior

**Relationship to Other Principles:**
- LSP violations often surface when ISP is violated (fat interface forces inappropriate implementations)
- Careful LSP enables OCP — safe polymorphic extension

---

## Interface Segregation Principle (ISP)

**Definition:** No client should be forced to depend on methods it does not use.

**Violation Indicators:**
- Interface with >5 methods
- Implementors that leave methods empty or throw `NotImplementedException`
- Class implementing an interface but only using 2 of 7 methods
- Multiple unrelated method groups in one interface

**Examples:**
- `UserServiceInterface` with `register()`, `login()`, `resetPassword()`, `updateProfile()`, `deleteAccount()`, `exportData()` — should be split
- Repository interface with both read and write methods when some consumers only read

**Recommended Refactoring:**
- Split interface into focused role interfaces (e.g., `Readable`, `Writable`, `Deletable`)
- A class can implement multiple small interfaces

**Relationship to Other Principles:**
- ISP at the interface level mirrors SRP at the class level
- Supports DIP by keeping interfaces focused

---

## Dependency Inversion Principle (DIP)

**Definition:** High-level modules should not depend on low-level modules. Both should depend on abstractions.

**Violation Indicators:**
- `new ConcreteClass()` inside a method (except value objects and DTOs)
- Method parameter typed to a concrete class where an interface exists
- Direct use of framework internals instead of abstractions
- Import/use of implementation classes in high-level service code

**Examples:**
- Service class that directly instantiates an HTTP client instead of injecting an HTTP interface
- Business logic class that directly references a concrete ORM model instead of a repository interface
- Hard dependency on a caching facade instead of an injected cache interface

**Recommended Refactoring:**
- Introduce an interface, bind implementation in the dependency container
- Use constructor injection via the DI container
- Replace `new` with factory or container resolution

**Relationship to Other Principles:**
- Enables OCP by allowing new implementations without modifying consumers
- ISP ensures the abstractions clients depend on are appropriately sized
