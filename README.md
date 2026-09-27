# LLD Interview Preparation (C# / .NET)

Low-level design practice for software engineering interviews.

This repo grows **one topic at a time**. Each folder is added only after that step is taught and attempted. Solutions are added only after an attempt.

## How we work

```text
Understand → Model → Design → Justify → Code → Extend
```

For every concept:

1. Simple explanation
2. Real-world analogy
3. Why it exists
4. Small C# example
5. Where it appears in an LLD problem
6. A small exercise
7. An interview-style problem (attempt first, solution after)

Language: interview-readable C#. Patterns are used only when they solve a real problem.

## Learning order

```text
0.  Assessment                         (in progress)
1.  OOP revision
2.  SOLID
3.  Relationships / UML
4.  Composition vs inheritance
5.  Interfaces / abstractions
6.  Dependency injection
7.  Remaining design patterns
8.  Domain modeling
9.  State machines
10. Concurrency
11. Error handling / idempotency
12. Beginner LLD problems
13. Intermediate LLD problems
14. Advanced LLD problems
15. Pattern recognition
16. Mock interviews
```

The order can change if the assessment shows a weak area.

## Roadmap

### Level 1 — OOP foundation

Class, object, interface, abstract class, encapsulation, abstraction, inheritance, polymorphism, composition, aggregation, association, dependency, constructor, access modifiers, static vs instance, immutability, value objects, entity vs service, DTO vs domain object.

Focus: why each idea matters when you design objects, not only how the syntax works.

### Level 2 — SOLID

Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, Dependency Inversion.

Focus: how to spot a violation while designing, with bad code, improved code, an LLD example, and a common mistake for each principle.

### Level 3 — Object relationships

Association, aggregation, composition, inheritance, interface implementation, dependency.

Focus: who creates an object, who owns it, who shares it, and why.

### Level 4 — UML for interviews

Class diagram, interface, inheritance, association, composition, aggregation, dependency, multiplicity. ASCII diagrams only. How to sketch a design quickly on a whiteboard.

### Level 5 — Design patterns

Already familiar (light revision only): Singleton, Builder, Strategy, Factory, Abstract Factory.

To learn in depth:

- Creational: Prototype
- Structural: Adapter, Decorator, Facade, Proxy, Composite, Bridge
- Behavioral: Observer, Command, State, Template Method, Chain of Responsibility, Iterator, Mediator, Memento

Each pattern: problem, code without the pattern, improved design, diagram, C#, real example, LLD example, when not to use it, how to recognize it, and a comparison with a similar pattern.

### Level 6 — LLD ideas beyond patterns

Dependency injection, dependency inversion, composition over inheritance, encapsulation of state, immutability, domain modeling (entity, value object, service, repository).

### Level 7 — Turning a problem into a design

```text
Problem → Clarify → Actors → Objects → Relationships → Responsibilities
→ Interfaces → Classes → SOLID → Patterns → Edge cases → Concurrency
→ C# → Extensions
```

### Level 8 — Requirement gathering

Actors, operations, constraints, concurrency, persistence, pricing, notification, multiple implementations, extensibility, failure behavior.

### Level 9 — Identifying classes

Which nouns become classes, and which nouns should stay as data, enums, or methods.

### Level 10 — Responsibility assignment

Which class owns which logic. Cohesion. Avoiding god classes and god methods.

### Level 11 — Interfaces

When an interface helps, and when it is an extra layer with one implementation.

### Level 12 — Enum vs class vs interface

Decision rules for types such as vehicle type, payment type, customer tier, and notification type.

### Level 13 — State management

States, transitions, and events. `if/else` versus the State pattern.

### Level 14 — Observer / events

C# events and delegates, and where Observer fits.

### Level 15 — Command

Remote control, then trading orders: place, cancel, modify.

### Level 16 — Chain of Responsibility

A pipeline of checks, and how it relates to ASP.NET Core middleware.

### Level 17 — Concurrency

Race conditions, locks, `ConcurrentDictionary`, atomic updates, deadlock. Applied to inventory, parking, seat booking, and bank withdrawals.

### Level 18 — Problems (attempt, then review, then ideal solution)

Beginner: Parking Lot, Vending Machine, Library, ATM, Coffee Machine, Tic Tac Toe, Snake and Ladder, Deck of Cards.

Intermediate: Elevator, Movie Booking, Restaurant Reservation, Splitwise, Car Rental, Hotel Booking, Ride Sharing, Chess, Logger, Notification, File System, Meeting Room Scheduler.

Advanced: Rate Limiter, Cache, LRU Cache, Pub/Sub, Message Queue, Job Scheduler, Distributed Lock, concurrent Parking Lot, Inventory, Order Management, Payment Processing, Trading/Order Management.

### Level 19–32 — Interview practice

Evaluation rubric, coding style, DI wiring, repository boundary, errors, idempotency, extensibility questions, pattern judgment (required / optional / overengineering), 50 recognition drills, rapid-fire questions, mock interviews, code review, cheat sheets, and one master cheat sheet at the end.

## Repo layout

```text
00-assessment/     questions, one file at a time
01-oop/            filled when we reach that level
...
```

Current step: **Assessment, question 1**.
