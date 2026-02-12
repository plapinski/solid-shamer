# Code Smells Catalog — Reference

Organized by Martin Fowler's categories from "Refactoring: Improving the Design of Existing Code."

---

## Bloaters

Code that has grown too large to manage effectively.

### Long Method
**Detection:** Method >20 lines of logic (excluding boilerplate). Multiple levels of indentation. Need to scroll to understand the full flow.
**Fix:** Extract Method, Replace Temp with Query, Decompose Conditional, Replace Method with Method Object.

### Large Class
**Detection:** Class >300 lines. Multiple unrelated method groups. Vague class name (Manager, Handler, Processor). Too many instance variables.
**Fix:** Extract Class, Extract Subclass, Extract Interface.

### Long Parameter List
**Detection:** Method accepts >4 parameters. Parameters frequently passed together in multiple methods.
**Fix:** Introduce Parameter Object, Preserve Whole Object, Replace Parameter with Method Call.

### Primitive Obsession
**Detection:** Using strings for structured data (email, phone, currency). Type-coded fields instead of objects. String constants for behavior selection.
**Fix:** Replace Data Value with Object, Replace Type Code with Class, Replace Type Code with Subclass/Strategy.

### Data Clumps
**Detection:** Same group of fields/parameters appears in multiple classes or methods. Related values passed separately instead of as an object.
**Fix:** Extract Class (for fields), Introduce Parameter Object (for parameters), Preserve Whole Object.

---

## Object-Orientation Abusers

Patterns that misuse or underuse OO capabilities.

### Switch Statements
**Detection:** switch/match on type discriminator. Same conditional repeated across multiple methods. Adding a new type requires modifying existing code.
**Fix:** Replace Conditional with Polymorphism, Replace Type Code with Strategy, Replace Type Code with State.

### Parallel Inheritance Hierarchies
**Detection:** Every time you add a subclass to one hierarchy, you need to add a corresponding subclass to another. Prefixes match between hierarchies.
**Fix:** Move Method and Move Field to collapse one hierarchy into the other.

### Refused Bequest
**Detection:** Subclass only uses a fraction of inherited methods. Subclass overrides methods to no-op. Subclass throws on inherited methods.
**Fix:** Replace Inheritance with Delegation, Extract Superclass with only the shared behavior.

### Alternative Classes with Different Interfaces
**Detection:** Two classes that do the same thing but with different method names. Interchangeable functionality behind incompatible APIs.
**Fix:** Rename Method to align, Extract Superclass or Interface, Move Method to consolidate.

---

## Change Preventers

Patterns that make changes harder than they should be.

### Divergent Change
**Detection:** One class changes frequently for multiple, unrelated reasons. Different parts of the class change on different schedules or for different stakeholders.
**Fix:** Extract Class — split the class along its axes of change.

### Shotgun Surgery
**Detection:** A single logical change requires editing many classes. Adding a field to a concept means touching 5+ files. Inverse of Divergent Change.
**Fix:** Move Method and Move Field to consolidate related logic. Use Inline Class to collapse thin classes.

### Feature Envy
**Detection:** A method uses another class's data more than its own. Chains of getters from another object to perform calculations. Method would be more natural on the other class.
**Fix:** Move Method to the class it's envious of. If only part of the method is envious, Extract Method first, then move.

---

## Dispensables

Code that is unnecessary and could be removed.

### Dead Code
**Detection:** Methods never called. Parameters never used. Variables assigned but never read. Commented-out code blocks. Unreachable branches.
**Fix:** Remove. Use version control history if you need it later.

### Speculative Generality
**Detection:** Abstract classes with only one subclass. Interfaces with only one implementation (and no test doubles). Unused parameters "for future use." Helper methods called from one place.
**Fix:** Collapse Hierarchy, Inline Class, Remove Parameter, Rename Method to remove speculation.

### Lazy Class
**Detection:** Class with only 1-2 trivial methods. Class that simply delegates all work to another class. Class created "for the architecture" but carrying no weight.
**Fix:** Inline Class into its caller. Collapse Hierarchy if it's a near-empty subclass.

### Duplicate Code
**Detection:** Identical logic blocks in sibling methods. Same algorithm in multiple classes with minor variations. Copy-pasted blocks.
**Fix:** Extract Method (same class), Extract Class (different classes), Pull Up Method (subclasses), Form Template Method (similar algorithms).

### Comments as Deodorant
**Detection:** Comments explaining what the code does (rather than why). Comments compensating for poor naming. Long comment blocks before complex logic.
**Fix:** Extract Method with a descriptive name, Rename Variable/Method, simplify the logic. Keep "why" comments; eliminate "what" comments.

---

## Couplers

Patterns that create excessive coupling between classes.

### Feature Envy
**Detection:** (See Change Preventers above — listed in both categories by Fowler.)

### Inappropriate Intimacy
**Detection:** Classes that access each other's private/protected members. Bidirectional references between classes. One class reaching into another's internal data structure.
**Fix:** Move Method, Move Field to consolidate. Extract Class as an intermediary. Replace Inheritance with Delegation.

### Message Chains
**Detection:** `a.getB().getC().getD().doSomething()` — long chains navigating an object graph. Client knows the internal structure of intermediate objects.
**Fix:** Hide Delegate — add a method on the immediate collaborator. Restructure so the caller gets what it needs directly.

### Middle Man
**Detection:** Class where most methods simply delegate to another single class. Wrapper that adds no value. Class exists only to forward calls.
**Fix:** Remove Middle Man — let the caller talk directly to the delegate. If some delegation is valuable, keep those methods and inline the rest.

### Incomplete Library Class
**Detection:** Library class is almost right but missing a method you need. Workaround code wrapping library calls.
**Fix:** Introduce Foreign Method (single method), Introduce Local Extension (multiple methods), or wrap with an Adapter.
