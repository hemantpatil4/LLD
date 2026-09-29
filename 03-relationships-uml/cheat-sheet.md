# Level 3 cheat sheet — Relationships and UML

```text
ASSOCIATION
Problem: two independent objects have a lasting link.
Recognition: field points at the other; both can exist alone.
Diagram: Customer 1 ---- * Order
C#: List<Order> on Customer
Common mistake: calling every link composition.

AGGREGATION
Problem: whole holds parts that outlive the whole.
Recognition: part created outside; can join another whole.
Diagram: Department <>---- Professor
C#: Add(Professor professor)
Common mistake: deleting the professor with the department.

COMPOSITION
Problem: part has no meaning without the whole; not shared.
Recognition: created inside, or fully owned after handoff.
Diagram: ParkingLot <*>---- Floor
C#: _floors.Add(new Floor(...)) inside the lot
Common mistake: sharing one engine across two cars.

DEPENDENCY
Problem: method needs an object only for this call.
Recognition: parameter in, used, not stored.
Diagram: Printer - - -> Ticket
C#: void Print(Ticket ticket)
Common mistake: storing every parameter as a field.

INJECTED INTERFACE
Problem: workflow must not name a vendor.
Recognition: constructor takes IPaymentMethod.
Diagram: ExitGate ----> IPaymentMethod
C#: ExitGate(IPaymentMethod payment)
Common mistake: injecting Ticket when the service should create it.

OWNERSHIP QUESTIONS
1. Part meaningful alone?
2. Shared by two wholes?
3. Dies with the whole?
4. Need to swap for test or vendor?

UML SYMBOLS
----      association
<>----    aggregation
<*>----   composition
--|>      inheritance
- - ->    dependency
1, 0..1, *, 1..*   multiplicity

WHITEBOARD ORDER
Story -> actors -> nouns -> ownership lines -> interfaces -> key methods -> multiplicity

COMPOSITION VS INHERITANCE
is-a always true?                 -> inheritance
need a part or swappable behavior? -> composition
inherited only to reuse a method?  -> prefer composition
child cannot keep parent promise?  -> do not inherit that parent
```
