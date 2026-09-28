# I — Interface Segregation

## Simple explanation

An interface should be a small promise that its implementers actually keep. Split an interface when different classes need different subsets of the methods.

## Analogy

A dining menu that forces every guest to take every dish wastes food and annoys the guest. Separate menus match what people actually eat.

## Why it exists in LLD

A fat interface spreads fake methods through the design. Classes grow empty bodies or `throw new NotSupportedException()`. Callers cannot tell, from the type, which methods are real. That brings back the type checks LSP was meant to remove.

## Bad code

```csharp
public interface IMachine
{
    void Park(string plate);
    void TakePayment(decimal amount);
    void SendEmail(string message);
    void GenerateDailyReport();
}

public class EntryGate : IMachine
{
    public void Park(string plate) { }
    public void TakePayment(decimal amount) => throw new NotSupportedException();
    public void SendEmail(string message) => throw new NotSupportedException();
    public void GenerateDailyReport() => throw new NotSupportedException();
}
```

## Why it is bad

An entry gate parks cars. The interface also demands payment, email, and reporting. Three methods are lies. A new method on `IMachine`, such as `PrintInvoice`, must be added to the gate even though the gate will never print invoices.

## Improved code

```csharp
public interface IParkingPoint
{
    void Park(string plate);
}

public interface IPaymentPoint
{
    void TakePayment(decimal amount);
}

public interface IReportSource
{
    void GenerateDailyReport();
}

public class EntryGate : IParkingPoint
{
    public void Park(string plate) { }
}

public class ExitGate : IParkingPoint, IPaymentPoint
{
    public void Park(string plate) { }
    public void TakePayment(decimal amount) { }
}
```

Each class implements the promises it can keep. `ExitGate` implements two interfaces because it really does both jobs. That is normal. The problem is one interface that mixes unrelated jobs.

## LLD example

`IParkingSpot` with `Park`, `StartCharging`, and `Wash`. A plain spot should not implement charging. Split `IChargeableSpot` for spots that have a cable.

A notification interface with `SendEmail`, `SendSms`, and `SendWhatsApp` forces the email class to pretend it sends WhatsApp. Prefer `INotificationSender` with one `Send` method, or separate interfaces if the calls are genuinely different.

## How to spot an ISP violation while designing

Look at each implementing class:

- an empty method
- `NotSupportedException`
- a comment that says "not used"

Look at the interface name. If the honest name is `IParkAndPayAndEmailAndReport`, it is several interfaces.

Look at callers. If one caller uses only `Park` and another uses only `GenerateDailyReport`, they do not share a concept.

Keep one interface when every implementer needs every method. `IPaymentMethod.Pay` plus `Refund` can stay together if every payment type refunds. Split when a subset of types cannot refund.

## Interview question

"Why does the entry gate not implement `TakePayment`?"

Because the type system should make that call impossible, not because the method throws.

## Common mistake

One interface per method, always. `Work` and `Eat` can live together if every worker does both. Segregation follows the split in implementers, not a quota of one method each.
