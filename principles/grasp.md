# GRASP Patterns — Reference

General Responsibility Assignment Software Patterns — 9 patterns for assigning responsibilities to classes.

---

## Information Expert

**Definition:** Assign responsibility to the class that has the information needed to fulfill it.

**When to Apply:** When deciding which class should own a method or computation.

**Example:**
```
// Violation: Controller calculates total outside Order
total = 0
for item in order.items:
    total += item.price * item.quantity

// Correct: Order has the data, Order calculates
total = order.calculateTotal()
```

**Violation Indicators:**
- Method fetches data from another object to perform logic on it
- Business logic in controllers or commands operating on model data
- Utility classes that take objects as parameters and operate on their fields

**Resolution:** Move the method to the class that holds the data.

---

## Creator

**Definition:** Assign class B the responsibility to create instances of class A if B contains A, aggregates A, closely uses A, or has the initializing data for A.

**When to Apply:** When deciding where `new` calls should live.

**Example:**
```
// Violation: Controller creates OrderItem directly
item = new OrderItem(productId, quantity, price)

// Better: Order creates its own items
order.addItem(productId, quantity, price)
```

**Violation Indicators:**
- Object creation scattered across unrelated classes
- Classes creating objects they don't aggregate or compose
- Complex multi-step construction not encapsulated

**Resolution:** Move creation to the containing/aggregating class, or introduce a Factory.

---

## Controller

**Definition:** Assign responsibility for handling a system event to a class that represents the overall system or a use case.

**When to Apply:** When deciding which object receives and coordinates a system operation.

**Example:**
```
// Violation: Controller contains business logic
class OrderController:
    function store(request):
        // 30 lines of validation, calculation, persistence, notification

// Better: Controller delegates to use-case handler
class OrderController:
    function store(request, createOrderAction):
        return createOrderAction.execute(request.validated())
```

**Violation Indicators:**
- Controller action >15 lines of logic
- Controller injecting >2 services
- Controller directly manipulating multiple models

**Resolution:** Extract business logic into Action classes or Service methods.

---

## Low Coupling

**Definition:** Assign responsibilities to minimize dependencies between classes.

**When to Apply:** When choosing between designs that differ in how many classes reference each other.

**Example:**
- Class references >5 concrete classes
- Changing one class forces changes in many others
- Circular dependency chains

**Violation Indicators:**
- High fan-out: many imports of concrete classes
- Changing a method signature breaks callers across multiple files (Shotgun Surgery)
- Two classes referencing each other (circular dependency)

**Resolution:** Introduce interfaces, use dependency injection, apply Mediator or Observer patterns.

---

## High Cohesion

**Definition:** Assign responsibilities so that a class's methods are closely related and focused.

**When to Apply:** When evaluating whether a class has too many unrelated responsibilities.

**Example:**
- Methods operate on disjoint subsets of class properties
- Some methods could be moved to another class without losing anything
- Class name is vague (e.g., `Helper`, `Manager`, `Utility`)

**Violation Indicators:**
- Methods cluster into groups that don't share instance variables
- Class has both read and write operations for unrelated domains
- Difficult to name the class with a specific noun

**Resolution:** Extract cohesive groups of methods into separate classes.

---

## Polymorphism

**Definition:** When behavior varies by type, assign responsibility using polymorphic operations rather than conditionals.

**When to Apply:** When a method contains type-based conditional logic.

**Example:**
```
// Violation: switch on type
switch notification.channel:
    case "email": sendEmail(notification)
    case "sms": sendSms(notification)
    case "push": sendPush(notification)

// Better: polymorphic dispatch
notification.channel().send(notification)
```

**Violation Indicators:**
- switch/match on a type field repeated across methods
- if-else chain checking `instanceof`
- Same discriminator used in multiple methods

**Resolution:** Replace Conditional with Polymorphism — introduce an interface, one implementation per variant.

---

## Pure Fabrication

**Definition:** Assign responsibility to an artificial class that doesn't represent a domain concept, to achieve low coupling and high cohesion.

**When to Apply:** When no domain class is a natural fit for a responsibility, and assigning it would decrease cohesion.

**Examples:** Repository classes, Service classes, Factories, Formatters, Mappers.

**Note:** Common in most frameworks — services, actions, and jobs are all pure fabrications.

---

## Indirection

**Definition:** Assign responsibility to an intermediate object to mediate between components and reduce direct coupling.

**When to Apply:** When two classes shouldn't know about each other directly.

**Examples:** Event dispatchers, message queues, middleware, adapters.

**Note:** Framework event systems, queue workers, and middleware pipelines are all indirection patterns.

---

## Protected Variations

**Definition:** Design to protect elements from variations in other elements by wrapping the focus of instability with an interface.

**When to Apply:** When you identify a point of likely change or variation.

**Examples:** Repository interfaces (protect against ORM changes), notification channel interfaces (protect against new channels), payment gateway interfaces.

**Violation Indicators:**
- Direct dependency on something likely to change (external API, third-party SDK)
- No abstraction around integration points
- Concrete references to volatile dependencies
