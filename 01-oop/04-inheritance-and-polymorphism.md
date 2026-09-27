# Inheritance and polymorphism

## Inheritance

### Simple explanation

**Inheritance** means a child class is a more specific version of a parent class. The child receives the parent's fields and methods, and can add or replace behavior.

People say this is an **is-a** relationship. A `Bike` is a `Vehicle`.

### Analogy

A savings account is a bank account. It still has a balance and a deposit action. It also has an interest rule the plain account form did not spell out.

### Why we need it in LLD

Shared facts should be written once: every vehicle has a plate, every chess piece has a color and a square. The child exists because the specific kind adds something true of that kind.

### Small C# example

```csharp
public class Account
{
    public string Id { get; }
    public decimal Balance { get; protected set; }

    public Account(string id) => Id = id;

    public void Deposit(decimal amount) => Balance += amount;
}

public class SavingsAccount : Account
{
    public decimal InterestRate { get; }

    public SavingsAccount(string id, decimal interestRate) : base(id)
    {
        InterestRate = interestRate;
    }
}
```

Important lines:

- `: Account` means `SavingsAccount` is an `Account`.
- `protected set` lets the child change `Balance`. Other classes still cannot.
- `: base(id)` sends the id to the parent constructor.

### Where it appears

`Bike` and `Car` under `Vehicle`. `King` and `Pawn` under `Piece`. Use it when the sentence "child is a parent" stays true for every object, including future ones.

### When it becomes a bad fit

A `ParkingSpot` is not a `Vehicle`. A spot holds a vehicle. That is a has-a relationship, covered in the relationships lesson. Forcing it into inheritance makes the model lie.

Keep inheritance shallow. One parent is enough for most interview designs. A tree of five levels is usually a sign that the child classes are not truly the same kind of thing.

## Polymorphism

### Simple explanation

**Polymorphism** means one call can do different work depending on which object is really there.

You call `Fits(...)` or `Pay(...)`. A bike answers one way. A bus answers another way. The caller does not keep a chain of type checks.

### Analogy

A dealer says "quote this." The spot desk, the forward desk, and the options desk each quote in their own way. The dealer uses the same verb.

### Why we need it in LLD

New kinds arrive during the "what if we add X?" part of the interview. If the parking lot is full of `if (vehicle is Bike) ... else if (vehicle is Car)`, every new vehicle edits the lot. If each vehicle answers `Fits` itself, the lot stays closed to that change.

### Small C# example

```csharp
public class ExitGate
{
    public void Charge(IPaymentMethod payment, decimal fee)
    {
        payment.Pay(fee);
    }
}
```

Pass a `CardPayment` object and a card is charged. Pass a `UpiPayment` object and UPI is charged. `ExitGate.Charge` stays the same. That is polymorphism: same call, different object, different work.

### Where it appears

Piece movement in chess, vehicle-to-spot matching, payment at the gate, notification channels, pricing rules.

### Inheritance and polymorphism together

Inheritance shares the blueprint. Polymorphism is the payoff: a `List<Vehicle>` can hold a bike and a car, and `vehicle.Fits(spot)` runs the method of the real object.
