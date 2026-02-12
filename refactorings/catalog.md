# Refactoring Techniques Catalog — Reference

Organized by category from Martin Fowler's refactoring catalog.

---

## Composing Methods

Techniques for organizing code within methods.

### Extract Method
**Trigger:** Long Method, Duplicate Code, Comments explaining a block.
**Technique:** Take a code fragment, create a method with an intention-revealing name, replace the fragment with a call. Pass needed variables as parameters, return computed values.

### Inline Method
**Trigger:** Method body is as clear as its name. Excessive delegation through trivial methods.
**Technique:** Replace method calls with the body, then remove the method. Use when indirection obscures rather than clarifies.

### Replace Temp with Query
**Trigger:** Temporary variable assigned once, used to hold an expression result.
**Technique:** Extract the expression into a method, replace all references to the temp with a method call. Enables further extraction and reuse.

### Introduce Explaining Variable
**Trigger:** Complex expression that's hard to understand.
**Technique:** Put the result of a complex expression (or parts of it) into a temporary variable with a descriptive name. Prefer Extract Method if possible.

### Replace Method with Method Object
**Trigger:** Long method with many local variables that prevent extraction.
**Technique:** Create a new class, move the method there as `execute()`, turn local variables into fields. Now you can extract methods freely within the new class.

---

## Moving Features Between Objects

Techniques for placing responsibilities correctly.

### Move Method
**Trigger:** Feature Envy — method uses another class's data more than its own.
**Technique:** Create a method in the target class, move the body, adjust references. The old method can delegate or be removed.

### Move Field
**Trigger:** A field is used more by another class than by its own.
**Technique:** Create the field in the target class, update all references. Often accompanies Move Method.

### Extract Class
**Trigger:** Large Class, Divergent Change — class has multiple responsibilities.
**Technique:** Create a new class, move relevant fields and methods. The original class delegates to the extracted class.

### Inline Class
**Trigger:** Lazy Class — class does too little to justify its existence.
**Technique:** Move all features into another class, remove the empty shell. Inverse of Extract Class.

### Hide Delegate
**Trigger:** Message Chains — client navigates through an object to reach a delegate.
**Technique:** Create a method on the server that delegates to the object, removing the client's knowledge of the chain.

### Remove Middle Man
**Trigger:** Middle Man — class with too many delegating methods.
**Technique:** Let the client call the delegate directly. Inverse of Hide Delegate. Balance is key.

---

## Organizing Data

Techniques for structuring data and associations.

### Replace Data Value with Object
**Trigger:** Primitive Obsession — data item that needs additional behavior or validation.
**Technique:** Turn the data value into an object (value object). Add behavior (validation, formatting, comparison) to the new class.

### Replace Type Code with Class
**Trigger:** Numeric or string type codes that don't affect behavior.
**Technique:** Create a class or enum for the type code. Replace the raw value with the typed object.

### Replace Type Code with Subclasses
**Trigger:** Type code that affects behavior (used in conditionals).
**Technique:** Create a subclass for each type code value. Move conditional behavior into overridden methods.

### Replace Type Code with Strategy/State
**Trigger:** Type code affects behavior but the type can change at runtime, or subclassing isn't possible.
**Technique:** Create a Strategy interface and implementations for each type. Delegate behavior to the strategy object.

### Encapsulate Collection
**Trigger:** Getter returns a raw collection that callers can modify directly.
**Technique:** Return a read-only view or copy. Provide add/remove methods on the owning class.

---

## Simplifying Conditional Expressions

Techniques for making conditionals clearer.

### Decompose Conditional
**Trigger:** Complex conditional with non-obvious test and lengthy branches.
**Technique:** Extract the condition into a method (`isEligibleForDiscount()`), extract each branch into a method.

### Consolidate Conditional Expression
**Trigger:** Multiple conditionals that lead to the same result.
**Technique:** Combine using logical operators, extract into a named method that explains the combined condition.

### Replace Conditional with Polymorphism
**Trigger:** Switch Statements smell — conditional choosing behavior based on type.
**Technique:** Create a class hierarchy or interface. Move each branch into an overriding method. Let polymorphism do the dispatch.

### Introduce Guard Clauses
**Trigger:** Deeply nested conditionals. Method has a main path obscured by edge-case handling.
**Technique:** Replace nested if-else with early returns for edge cases. The main path flows without indentation.

### Replace Nested Conditional with Guard Clauses
**Trigger:** Method with nested if-else that makes the normal path unclear.
**Technique:** Convert each special case into a guard clause that returns early. Keep the normal path as the unguarded remainder.

### Introduce Null Object
**Trigger:** Repeated null checks for the same object.
**Technique:** Create a class that implements the expected interface with default (no-op) behavior. Return this instead of null.

---

## Simplifying Method Calls

Techniques for improving method interfaces.

### Rename Method
**Trigger:** Method name doesn't reveal its purpose.
**Technique:** Choose a name that describes what the method does, not how. Update all callers.

### Introduce Parameter Object
**Trigger:** Long Parameter List, Data Clumps — same group of parameters passed together.
**Technique:** Create a class (DTO/value object) that groups the parameters. Replace the parameter list with the new object.

### Preserve Whole Object
**Trigger:** Extracting values from an object to pass as separate parameters.
**Technique:** Pass the whole object instead. The receiving method extracts what it needs. Reduces parameter counts and couples to the object's interface rather than its data.

### Replace Parameter with Method Call
**Trigger:** A parameter value can be obtained by calling a method the receiver already knows about.
**Technique:** Remove the parameter, have the method obtain the value itself. Simplifies the caller.

### Separate Query from Modifier
**Trigger:** Method both returns a value and changes state (CQS violation).
**Technique:** Split into two methods — one query (returns value, no side effects) and one modifier (changes state, void return).

---

## Dealing with Generalization

Techniques for managing class hierarchies.

### Pull Up Method
**Trigger:** Duplicate Code across sibling subclasses.
**Technique:** Move the identical method to the superclass. If methods are similar but not identical, extract the difference first (Template Method).

### Push Down Method
**Trigger:** Behavior on a superclass that's only relevant to one subclass.
**Technique:** Move the method down to the subclass that uses it.

### Extract Interface
**Trigger:** Multiple clients use the same subset of a class's methods. Need to decouple from a concrete class.
**Technique:** Create an interface with the shared methods. Have the class implement it. Type-hint callers to the interface.

### Collapse Hierarchy
**Trigger:** Superclass and subclass are too similar to justify two classes.
**Technique:** Merge them into one class. Remove the hierarchy level.

### Replace Inheritance with Delegation
**Trigger:** Refused Bequest — subclass only uses a fraction of inherited behavior.
**Technique:** Create a field for the parent class, delegate the methods you need, remove the inheritance relationship.

### Form Template Method
**Trigger:** Similar algorithms in sibling subclasses with the same structure but different details.
**Technique:** Extract the structure into a template method in the superclass. Override the varying steps in subclasses.
