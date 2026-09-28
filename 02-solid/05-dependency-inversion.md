# D — Dependency Inversion

## Simple explanation

A high-level workflow should depend on a promise. A low-level detail should implement that promise. Both point at the promise.

```text
High level (ExitGate)
        |
        v
   IPaymentMethod
        |
        v
Low level (CardPayment)
```

**High level** means the business workflow: exit a car, place an order, price a trade. **Low level** means a detail that can be swapped: Visa, UPI, SQL Server, SMTP, a market-data feed.

This is the rule. **Dependency injection** is one way to follow the rule: pass the object in through the constructor instead of constructing it inside.

```csharp
new CardPayment()                      // workflow builds the detail
ExitGate(IPaymentMethod payment)       // workflow receives the promise
```

## Analogy

A head cashier says "take payment." The cashier does not build the card machine. The shop plugs a card machine, or a UPI speaker, into the counter. The cashier's instructions stay the same.

## Why it exists in LLD

If the exit gate constructs `new CardPayment()`, three things go wrong:

- adding UPI edits the gate
- a unit test of the gate charges a real card, or fails because it cannot
- the business rule is coupled to a vendor

**Coupling** here means the gate's source code mentions the vendor class. A vendor change forces a gate change.

## Bad code

```csharp
public class ExitGate
{
    public void LetOut(decimal fee)
    {
        var payment = new CardPayment();
        payment.Pay(fee);
    }
}
```

The arrow points the wrong way: `ExitGate` knows `CardPayment`.

```text
ExitGate ----> CardPayment
```

## Why it is bad

`ExitGate` is a parking rule. `CardPayment` is a vendor detail. The rule now changes when the vendor changes. Tests need a real card path.

## Improved code

```csharp
public interface IPaymentMethod
{
    void Pay(decimal amount);
}

public class CardPayment : IPaymentMethod
{
    public void Pay(decimal amount) { }
}

public class ExitGate
{
    private readonly IPaymentMethod _payment;

    public ExitGate(IPaymentMethod payment)
    {
        _payment = payment;
    }

    public void LetOut(decimal fee)
    {
        _payment.Pay(fee);
    }
}
```

```text
ExitGate --> IPaymentMethod
                 ^
                 |
            CardPayment
```

`ExitGate` mentions only `IPaymentMethod`. `CardPayment` is plugged in at the edge of the program, in `Main` or in the composition root:

```csharp
IPaymentMethod payment = new CardPayment();
var gate = new ExitGate(payment);
```

The design does not need ASP.NET Core. A later wiring example can be `services.AddScoped<IPaymentMethod, CardPayment>()`. The LLD is the constructor above.

## LLD example

Parking exit and payment. Order placement and a repository. Pricing and a rate feed. Notifications and an email sender. The workflow names `IRateProvider` or `IOrderRepository`. SQL and HTTP stay in the classes that implement those interfaces.

## How to spot a DIP violation while designing

Search the workflow class for:

- `new SomeService()` or `new SomeRepository()`
- type names that are technology or vendors: `SqlConnection`, `SmtpClient`, `KafkaProducer`, `StripeClient`

Ask: "Can I test this class by passing a stand-in?" If constructing the class starts the real database or the real card network, the dependency points at the detail.

Do not invert every `new`. These are fine inside the workflow:

- `new Ticket(...)`
- `new Money(amount, currency)`
- `new List<ParkingSpot>()`

Those are values and entities the workflow owns. Invert the dependencies that vary or that talk to the outside world.

The interface should be defined near the workflow's need ("something that can take payment"), and the vendor class should implement it. If the interface is `ICardPayment` with card-only methods, the gate is still tied to cards. That is an interface that failed to invert anything useful.

## Interview question

"What would you change to add UPI, and what would you not change?"

Add `UpiPayment : IPaymentMethod`. Change the one line that chooses the object. Do not edit `ExitGate.LetOut`.

## Common mistake

An interface for every class, including `ITicket` for a stable entity. DIP targets the volatile details. Another mistake is confusing the names: injection is the plumbing, inversion is the direction of the arrow.
