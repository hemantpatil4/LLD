# S — Single Responsibility

## Simple explanation

A class should have one job. One reason for a developer to open it and change it.

**Cohesion** means the fields and methods in a class belong to that same job. High cohesion is what SRP looks like in the code.

**Coupling** means how much one class has to know about another. A class with many jobs is usually coupled to many unrelated things: the database, the SMS gateway, and the fee rules.

## Analogy

A bank teller takes deposits. A separate person repairs the ATM. A separate system sends the SMS. If one employee does all three, a new SMS vendor and a new deposit rule both interrupt that same person.

## Why it exists in LLD

Interview designs rot when one class owns the whole story. A change to the fee formula can break parking, messaging, or saving. You also cannot explain the design in sentences. "This class parks cars and calculates fees and sends SMS and writes SQL" is a signal to split.

## Bad code

```csharp
public class ParkingLot
{
    public void Park(string plate, string vehicleType) { }
    public decimal CalculateFee(string vehicleType, int hours) { }
    public void SendSms(string phone, string message) { }
    public void SaveTicketToDatabase(string plate) { }
}
```

## Why it is bad

Four unrelated changes edit this class:

- a new parking rule
- a new fee formula
- a new SMS vendor
- a new table or column

You cannot test the fee formula without dragging in SMS and SQL. This is a **god class**: one class that knows the whole system.

## Improved code

```csharp
public class Ticket
{
    public string Plate { get; }
    public DateTime EntryTime { get; }

    public Ticket(string plate, DateTime entryTime)
    {
        Plate = plate;
        EntryTime = entryTime;
    }
}

public class FeeCalculator
{
    public decimal Calculate(string vehicleType, int hours)
    {
        return hours * 10m;
    }
}

public interface INotificationSender
{
    void Send(string phone, string message);
}

public class ParkingService
{
    private readonly FeeCalculator _fees;
    private readonly INotificationSender _notifications;

    public ParkingService(FeeCalculator fees, INotificationSender notifications)
    {
        _fees = fees;
        _notifications = notifications;
    }

    public Ticket Park(string plate)
    {
        return new Ticket(plate, DateTime.UtcNow);
    }
}
```

`Ticket` owns ticket facts. `FeeCalculator` owns the fee formula. `INotificationSender` owns delivery. `ParkingService` only coordinates: it asks the others to do their job.

## LLD example

Parking lot, order placement, ATM withdrawal. The workflow class may call other objects. It does not also format the receipt, talk to Visa, and write SQL.

## How to spot an SRP violation while designing

Say the class's job in one sentence with no "and."

Then list why it would change in the next year:

| Change | Belongs in |
| --- | --- |
| Fee policy | fee type |
| SMS vendor | notification type |
| Table shape | repository |
| Park / exit sequence | a small service |

Two rows pointing at the same class means split.

Also split when:

- the class name is `Manager`, `Processor`, or `System` and it holds most of the methods
- a public method uses none of the class's other data and could move out as-is

Keep methods together when they change for the same reason. An `Order` that checks a positive amount and stores its lines has one job: be a valid order. Splitting that into five classes is the common mistake.

## Interview question

"Why is this class not also sending the notification?"

A strong answer names the reason to change, not "because SOLID says so."

## Common mistake

Making a class per method when those methods change together. SRP is about one reason to change, not about the smallest possible file.
