# Relationship decisions

Level 1 introduced the names. This lesson is about the interview decision:

> Who creates this object? Who owns it? Can it be shared?

## The four ownership choices

| Choice | Meaning | C# signal |
| --- | --- | --- |
| Created inside | The whole builds the part | `new Engine()` in the constructor |
| Created outside, then owned | Caller builds it, then hands ownership over | constructor parameter stored in a field, and not reused |
| Injected | Caller supplies a collaborator the class depends on | `IPaymentMethod payment` in the constructor |
| Shared | Several wholes can refer to the same object | aggregation or association; the part outlives the wholes |

Created inside and created outside-then-owned are both usually **composition**. Injected can be composition, association, or dependency, depending on whether you store it and whether you claim ownership. Shared is **aggregation** or **association**.

## Decision questions

Ask these in this order:

1. Does the part make sense without the whole?
2. Can two wholes share the same part at the same time?
3. When the whole is destroyed, should the part disappear?
4. Do I need to swap the collaborator for a test or a new vendor?

```text
Part meaningless without whole?     yes -> composition
Two wholes share the same part?     yes -> aggregation or association
Destroyed with the whole?           yes -> composition
Need to swap for a test or vendor?  yes -> inject an interface
```

## Engine examples

### Created inside — composition

```csharp
public class Car
{
    private readonly Engine _engine;

    public Car()
    {
        _engine = new Engine();
    }
}
```

```text
Car <*>---- Engine
```

Use when: a car always has its own engine, and no outsider needs a different engine.

### Created outside, then owned — still composition

```csharp
public class Car
{
    private readonly Engine _engine;

    public Car(Engine engine)
    {
        _engine = engine;
    }
}
```

Use when: tests want to pass a fake engine, or a factory builds engines. The car still owns that engine after the handoff. Do not pass the same engine into a second car.

### Injected interface — dependency inversion + lasting link

```csharp
public class ExitGate
{
    private readonly IPaymentMethod _payment;

    public ExitGate(IPaymentMethod payment)
    {
        _payment = payment;
    }
}
```

```text
ExitGate ----> IPaymentMethod
```

Use when: the collaborator is a swappable service. Ownership of the payment object often sits with the composition root, not with the gate. The relationship on the whiteboard is association or dependency on the interface.

### Shared — aggregation

```csharp
public class MeetingRoom
{
    private readonly List<Employee> _attendees = new();

    public void Add(Employee employee)
    {
        _attendees.Add(employee);
    }
}
```

```text
MeetingRoom <>---- Employee
```

Use when: employees exist before the booking and after the room is deleted. The room does not create them.

## Parking lot ownership map

```text
ParkingLot <*>---- Floor <*>---- ParkingSpot
     |
     | association
     v
   Ticket  ----> Vehicle
```

| Pair | Relationship | Why |
| --- | --- | --- |
| Lot and floor | Composition | A floor is part of this lot |
| Floor and spot | Composition | A spot is part of this floor |
| Ticket and vehicle | Association | Both exist as their own records for the visit |
| Spot and vehicle | Association while parked | The spot holds a reference; the vehicle is not a part of the spot |
| Exit gate and payment | Injected interface | Swap card for UPI without editing the gate |

## FX desk ownership map

```text
OrderService ----> IRateFeed
     |
     | creates
     v
   Order <*>---- OrderLine
     |
     | uses value
     v
   Money / CurrencyPair
```

| Pair | Choice | Why |
| --- | --- | --- |
| Order and order line | Composition | A line has no meaning outside that order |
| Order and money | Value used by order | Immutable value object, not a long-lived part |
| Service and rate feed | Injected | Vendor can change; tests need a fake feed |
| Customer and order | Association | Customer and order both survive independently |

## Common interview mistakes

- Creating `Professor` inside `Department` and deleting professors when the department closes.
- Sharing one `Engine` across two cars.
- Drawing composition for every arrow because the diamond looks important.
- Injecting everything, including `new Ticket()`, when the service should own ticket creation.

## Rule you can say out loud

> If the part dies with the whole and is not shared, I use composition. If the part has its own life, I use association or aggregation. If I need to swap a vendor or a test double, I inject an interface.
