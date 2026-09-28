# Level 2 cheat sheet — SOLID

```text
SINGLE RESPONSIBILITY
Problem: one class changes for unrelated reasons.
Recognition: the job sentence needs "and", or the name is Manager/Processor and it owns the whole story.
Key idea: one reason to change. High cohesion.
Typical C#: Ticket, FeeCalculator, NotificationSender, and a thin ParkingService
Example: parking that also sends SMS and writes SQL
Common mistake: a class per method when those methods change together.

OPEN/CLOSED
Problem: a new kind forces an edit in a working switch.
Recognition: if/else or switch on "car", "card", "email", plus a follow-up "what if we add X?"
Key idea: new behavior is a new class behind an existing interface.
Typical C#: IFeeRule, IPaymentMethod, INotificationSender
Example: CarFeeRule added without editing Calculate
Common mistake: a strategy interface when only one implementation will ever exist.

LISKOV SUBSTITUTION
Problem: code that works for the parent breaks when given a child.
Recognition: override throws, empty override, or the caller checks `is Child`.
Key idea: the child keeps the parent's promises about inputs, results, and failures.
Typical C#: IFlyingBird separate from IBird; CanFit before Park
Example: Square that sets both sides when Width is set; Penguin.Fly throws
Common mistake: thinking "it compiles, so substitution is fine."

INTERFACE SEGREGATION
Problem: a class is forced to implement methods it does not have.
Recognition: NotSupportedException, empty methods, an interface name full of unrelated verbs.
Key idea: split the promise along what implementers actually do.
Typical C#: IParkingPoint and IPaymentPoint
Example: EntryGate should not implement SendEmail
Common mistake: one interface per method even when every implementer needs all of them.

DEPENDENCY INVERSION
Problem: a workflow names a vendor, a database, or a gateway.
Recognition: new CardPayment(), SqlConnection, SmtpClient, Kafka producer inside the business class.
Key idea: workflow depends on an interface; the detail implements it.
Typical C#: ExitGate(IPaymentMethod payment)
Example: card and UPI both implement IPaymentMethod
Common mistake: ITicket for a stable entity, or calling constructor injection the principle. Injection is the plumbing. Inversion is the direction of the arrow.

SPOTTING ORDER
1. One-sentence job (SRP)
2. List of reasons to change (SRP)
3. Switch on a kind (OCP)
4. Child versus parent promise (LSP)
5. Methods some implementers cannot keep (ISP)
6. new of a service or a vendor type (DIP)
```
