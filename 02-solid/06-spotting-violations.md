# How to spot SOLID violations while designing

Use this before you name a pattern. The principles come first. A pattern is optional.

## One pass over a new class

1. Write one sentence: "This class's job is ___."
2. List the changes that would edit it: business rule, vendor, database, message text, workflow sequence.
3. Underline any `if` or `switch` on a kind of thing.
4. For every parent type, read what the method promises, then read what the child does.
5. For every interface, check whether each implementer can honestly implement every method.
6. Find every `new` of a service. Decide whether that service is a swappable detail.

## Quick map

| You see this | Likely principle | What to do |
| --- | --- | --- |
| One class parks, prices, texts, and saves | SRP | Split by reason to change. Keep a thin coordinator. |
| `if (type == "car")` and more types are expected | OCP | Move each branch behind an interface or an abstract method. |
| Child throws, or caller writes `if (x is Child)` | LSP | Narrow the parent promise, or stop using that parent. |
| `NotSupportedException` on an interface method | ISP | Split the interface along the methods people actually use. |
| `new CardPayment()` inside the workflow | DIP | Pass `IPaymentMethod` into the constructor. |

## A design you can practice on

```csharp
public class OrderService
{
    public void Place(string pair, decimal amount, string channel)
    {
        if (amount <= 0)
            throw new InvalidOperationException("Amount must be positive.");

        decimal rate = new ReutersRateFeed().Get(pair);
        decimal price = amount * rate;

        if (channel == "email")
            new SmtpEmailSender().Send("Order placed");
        else if (channel == "sms")
            new TwilioSmsSender().Send("Order placed");

        new SqlOrderRepository().Insert(pair, price);
    }
}
```

What is wrong, in design language:

- **SRP.** Pricing, notification, and storage are three reasons to change, all inside `Place`.
- **OCP.** A WhatsApp channel adds another `else if` in a method that already works.
- **DIP.** `ReutersRateFeed`, `SmtpEmailSender`, `TwilioSmsSender`, and `SqlOrderRepository` are constructed inside the workflow. A test of `Place` hits Reuters, SMTP, Twilio, and SQL Server.
- **LSP / ISP** are not the main issue in this snippet. Do not force them in.

A cleaner shape:

```text
OrderService
    | uses
    +--> IRateFeed
    +--> INotificationSender
    +--> IOrderRepository

EmailSender  implements INotificationSender
SmsSender    implements INotificationSender
```

`Order` itself, if you introduce it, still checks `amount <= 0`. That rule belongs to the entity. The service coordinates the feed, the sender, and the repository.

## What not to "fix"

- A single payment method and no plan for a second one: a direct call can be clearer than `IPaymentMethod` with one class.
- `new Ticket()` inside the service: the service owns that creation.
- Two methods that change for the same business reason: keep them in one class.
- A child class that only adds data and keeps every parent promise: that is ordinary inheritance, not an LSP bug.
