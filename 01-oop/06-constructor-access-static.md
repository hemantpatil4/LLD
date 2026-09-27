# Constructor, access modifiers, static vs instance

## Constructor

### Simple explanation

A **constructor** runs when the object is created. Its job is to start the object in a valid state. After `new` returns, the required facts are already set.

### Analogy

A bank will not hand you an account booklet with a blank name and a blank currency. Opening the account fills those in before you leave the counter.

### Why we need it in LLD

If a `Ticket` can exist with no entry time, later code must constantly ask "is this ticket real?" Put the required facts in the constructor, and illegal objects are never created.

### Small C# example

```csharp
public class Ticket
{
    public string Id { get; }
    public DateTime EntryTime { get; }
    public string PlateNumber { get; }

    public Ticket(string id, DateTime entryTime, string plateNumber)
    {
        Id = id;
        EntryTime = entryTime;
        PlateNumber = plateNumber;
    }
}
```

`new Ticket("T1", DateTime.UtcNow, "MH01AB1234")` is a complete ticket. There is no path that forgets the plate.

### Where it appears

Every entity you introduce in an interview: `ParkingSpot`, `Order`, `Money`, `Loan`. Required collaborators also arrive through the constructor. That is constructor injection, taught properly in the dependency-injection level.

## Access modifiers

### Simple explanation

An access modifier decides who is allowed to see a field or call a method.

| Modifier | Who can use it |
| --- | --- |
| `private` | Only code inside this class |
| `protected` | This class and a child class |
| `internal` | Code in the same project (assembly) |
| `public` | Any code |

### Analogy

The bank's vault code is private. The teller window is public. A child product team may see protected procedures that customers never see.

### Why we need it in LLD

`public decimal Balance` invites `Balance = -50000`. `private` plus a method is how encapsulation is enforced. `protected` is for a child class that must participate in the rule, such as a savings account applying interest. `public` is the small surface you are willing to support when a new requirement arrives.

### Small C# example

```csharp
public class ParkingSpot
{
    public string Id { get; }
    public bool IsOccupied { get; private set; }

    public ParkingSpot(string id) => Id = id;

    public void Park() => IsOccupied = true;
    public void Leave() => IsOccupied = false;
}
```

Callers use `Park` and `Leave`. They can read `IsOccupied`. They cannot assign it.

### Where it appears

Any class with a rule. In a whiteboard design, say out loud which methods are public. That list is the class's responsibility.

## Static vs instance

### Simple explanation

An **instance** member belongs to one object. Each ticket has its own `Id`.

A **static** member belongs to the class, shared by every object. There is one copy for the whole process.

### Analogy

Each teller has their own cash drawer (instance). The branch has one wall clock (static). Every teller sees the same clock. None of them owns a private clock.

### Why we need it in LLD

Static looks convenient for "there is only one parking lot." It then becomes a hidden global object. Tests interfere with each other, and a second lot cannot exist. Prefer an ordinary object, created in `Main` or by the composition root, unless the value is truly universal and unchanging, such as a constant.

Static methods that do not touch object data are fine for small pure helpers. Static mutable fields are the ones that hurt designs.

### Small C# example

```csharp
public class Order
{
    public static int CreatedCount;
    public string Id { get; }

    public Order(string id)
    {
        Id = id;
        CreatedCount++;
    }
}
```

`Id` is per order. `CreatedCount` is one number shared by every order. Two threads creating orders can lose increments. That is a reason to be careful, and a reason interviews dislike casual static state.

### Where it appears

Constants, pure functions, and occasionally a factory method like `Money.Usd(10)`. A parking lot, a board, a vending machine, and a rate limiter should be normal instances so each test can build its own.
