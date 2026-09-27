# Interface and abstract class

## Interface

### Simple explanation

An **interface** is a promise. It lists the actions a class must be able to perform. It does not say how those actions work.

```csharp
public interface IPaymentMethod
{
    void Pay(decimal amount);
}
```

Any class that writes `: IPaymentMethod` must provide `Pay`.

### Analogy

A wall socket promises "you can plug in here." The lamp does not care whether the electricity comes from the grid or a generator. It only cares that the socket matches the promise.

### Why we need it in LLD

The exit gate of a parking lot needs to take money. Today that might be a card. Tomorrow it might be UPI. The gate should call `Pay`. It should keep working when a new payment class is added, without the gate learning card networks or UPI apps.

That is the LLD reason: one caller, many possible workers, chosen later.

### Small C# example

```csharp
public interface IPaymentMethod
{
    void Pay(decimal amount);
}

public class CardPayment : IPaymentMethod
{
    public void Pay(decimal amount)
    {
        Console.WriteLine("Charged card " + amount);
    }
}

public class UpiPayment : IPaymentMethod
{
    public void Pay(decimal amount)
    {
        Console.WriteLine("Charged UPI " + amount);
    }
}
```

Important lines:

- `interface IPaymentMethod` is the promise. You cannot write `new IPaymentMethod()`.
- `: IPaymentMethod` means "this class keeps that promise."
- `Pay` is the only thing the gate needs to know.

### Where it appears

Parking exit, notification sender, rate provider, pricing rule. Any place where the interview hint is "we may add another way to do this."

### When an interface is extra

One `Customer` class, and no second way to "be a customer," does not need `ICustomer`. An interface earns its place when something else must depend on the promise, and more than one class may keep that promise.

## Abstract class

### Simple explanation

An **abstract class** is a half-finished blueprint. It can already contain fields and working methods. It can also declare a method that each child must finish.

You cannot create an object from the abstract class itself. You create an object from a child that finished the missing method.

### Analogy

A bank account opening form already has the bank logo, the account-number rules, and a `Deposit` procedure printed on it. The blank line "what extra rule does this product have?" is filled by Savings or Current.

### Why we need it in LLD

Several classes share real data and real code, and each one still has a custom piece. Copy-pasting the shared code into every class creates drift: one copy gets the bug fix, the others do not.

### Small C# example

```csharp
public abstract class Vehicle
{
    public string PlateNumber { get; }

    protected Vehicle(string plateNumber)
    {
        PlateNumber = plateNumber;
    }

    public abstract bool Fits(string spotSize);
}

public class Bike : Vehicle
{
    public Bike(string plateNumber) : base(plateNumber) { }

    public override bool Fits(string spotSize)
    {
        return spotSize == "small" || spotSize == "medium" || spotSize == "large";
    }
}
```

Important lines:

- `abstract class` means no `new Vehicle(...)`.
- `PlateNumber` and the constructor are shared. Every vehicle has them.
- `abstract bool Fits` means each child writes its own rule.
- `: base(plateNumber)` runs the shared constructor first.

### Where it appears

Vehicles in a parking lot, accounts in a bank, pieces in chess that all have a color and a position but move differently.

## How to choose

| Situation | Choose |
| --- | --- |
| Caller needs a swappable action, and the classes share no code | Interface |
| Classes share fields or working methods, and each adds a custom part | Abstract class |
| A class must be pluggable in several unrelated ways | Interfaces. A class can implement several. |
| You need one shared parent with data | Abstract class. A class has one parent. |

A parking `Vehicle` often starts as an abstract class because plate number and entry are shared. A `IPaymentMethod` stays an interface because card and UPI share a promise, not a pile of fields.
