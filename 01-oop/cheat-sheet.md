# Level 1 cheat sheet — OOP

```text
CLASS
Problem: a noun has data and actions that belong together.
Recognition: "every ticket has an id, an entry time, and a close action."
Key idea: blueprint, not a live thing.
Typical C#: class Ticket
Example: Vehicle, Ticket, ParkingSpot
Common mistake: making a class for a single number or a verb.

OBJECT
Problem: you need one real instance of the blueprint.
Recognition: "this Honda, this ticket 1042."
Key idea: new Ticket(...) creates one object with its own data.
Typical C#: var ticket = new Ticket(...)
Example: two FxOrder objects with different amounts
Common mistake: thinking the class itself holds one customer's data.

INTERFACE
Problem: the caller needs an action that several classes can perform differently.
Recognition: "card today, UPI tomorrow, the gate should stay the same."
Key idea: a promise of methods, no required shared fields.
Typical C#: IPaymentMethod.Pay
Example: INotificationChannel, IRateProvider
Common mistake: ICustomer when only one Customer exists.

ABSTRACT CLASS
Problem: several classes share fields and working methods, and each must finish one custom part.
Recognition: "every vehicle has a plate, and Fits is different per vehicle."
Key idea: half-finished blueprint. No new on the abstract type. One parent only.
Typical C#: abstract class Vehicle
Example: Account -> SavingsAccount, Piece -> Pawn
Common mistake: using a parent just to reuse one helper. Prefer a shared collaborator.

ENCAPSULATION
Problem: callers can put an object into an impossible state.
Recognition: a public field that any class can assign.
Key idea: private data, public methods that enforce rules.
Typical C#: Balance { get; private set; } plus Withdraw
Example: ParkingSpot.Park instead of IsOccupied = true
Common mistake: public setters on every property.

ABSTRACTION
Problem: the caller is forced to know every internal step.
Recognition: one class sequences boiler, pump, and valve instead of calling Brew.
Key idea: a small public story. The steps stay inside.
Typical C#: ExitGate calls payment.Pay(fee)
Example: ParkingLot.Park(vehicle)
Common mistake: treating abstraction and encapsulation as the same sentence.
Encapsulation protects state. Abstraction simplifies the call.

INHERITANCE
Problem: specific types repeat the same fields and methods.
Recognition: "a bike is a vehicle" stays true for every bike.
Key idea: is-a. Child gets the parent blueprint.
Typical C#: class Bike : Vehicle
Example: King : Piece
Common mistake: inheritance for has-a. A spot is not a vehicle.

POLYMORPHISM
Problem: a chain of if/else on type must be edited for every new type.
Recognition: one call, different work, depending on the real object.
Key idea: payment.Pay(fee) works for card or UPI.
Typical C#: a method on an interface or abstract class
Example: piece.Move, vehicle.Fits
Common mistake: a switch on type next to a class hierarchy that already has the method.

ASSOCIATION
Problem: two independent objects have a lasting link.
Recognition: a field points at the other object, and each can exist alone.
Key idea: Customer ---- Order
Example: Member and Loan
Common mistake: calling every link composition.

AGGREGATION
Problem: a whole holds parts that outlive the whole.
Recognition: the part is created outside and can move to another whole.
Key idea: Department <>---- Professor
Example: playlist and songs, employees and a room
Common mistake: deleting the professor when the department closes.

COMPOSITION
Problem: a part has no meaning without its whole.
Recognition: created inside, or fully owned, and not shared.
Key idea: Car <*>---- Engine, ParkingLot <*>---- Floor
Example: Board and Square, Order and OrderLine
Common mistake: sharing one engine across two cars.

DEPENDENCY
Problem: a method needs an object only for that call.
Recognition: parameter in, used, not stored.
Key idea: Printer - - -> Ticket
Example: Quote(order, rate)
Common mistake: storing every parameter as a field.

CONSTRUCTOR
Problem: objects can be created half-empty.
Recognition: required facts are set after new, by property assignment.
Key idea: required data and collaborators enter through the constructor.
Typical C#: new Ticket(id, entryTime, plate)
Example: Money(amount, currency) rejects a bad amount
Common mistake: a public parameterless constructor that allows an invalid ticket.

ACCESS MODIFIERS
Problem: every field is reachable from every class.
Key idea: private by default for state, public for the supported actions.
Typical C#: private set, protected for children, public for the API
Common mistake: public fields.

STATIC VS INSTANCE
Problem: one shared mutable global hides inside the class.
Recognition: a static parking lot, counter, or cache that every caller shares.
Key idea: instance data belongs to one object. Static data belongs to the class.
Example: Order.Id is instance. A constant currency code can be static.
Common mistake: static mutable state for "there is only one of these."

IMMUTABILITY
Problem: a shared object is edited by a caller who only borrowed it.
Recognition: Money, Address, quote, currency pair.
Key idea: no setters. Changes return a new object.
Typical C#: get-only properties, init, record, readonly
Common mistake: a Money class with a public Amount setter.

VALUE OBJECT
Problem: the same concept is compared by id even though only the values matter.
Recognition: two USD 10 amounts should be equal.
Key idea: equality by value, no identity.
Example: Money, Address, Coordinate, CurrencyPair
Common mistake: giving Money an id.

ENTITY
Problem: you need to track which business thing changed.
Recognition: lookup by id, state changes, same id afterwards.
Example: Customer, Order, Vehicle, Ticket
Common mistake: putting workflow-of-the-whole-system methods on the entity.

SERVICE
Problem: a job uses several entities and fits none of them.
Recognition: park, checkout, price, notify, settle.
Key idea: coordinates. Little or no identity.
Example: PricingService, ParkingService
Common mistake: one service that owns every rule. That is a god class.

DTO VS DOMAIN OBJECT
Problem: an API shape and a business rule share one class.
Recognition: request, response, Kafka message.
Key idea: DTO carries data. Domain object enforces rules.
Example: CreateOrderRequest versus Order
Common mistake: skipping the domain object and validating only in the controller.
```
