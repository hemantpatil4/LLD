# Composition vs inheritance

## Simple explanation

**Inheritance** means is-a. A bike is a vehicle. The child receives the parent's data and behavior.

**Composition** means has-a. A car has an engine. The whole holds a part and uses it.

**Composition over inheritance** means: when you need reusable behavior, prefer holding a collaborator. Use inheritance only when the child truly is the parent for every object, forever.

## Analogy

You need a camera on a phone.

Inheritance way: `PhoneWithCamera : Phone`. Tomorrow you need GPS and a fingerprint reader. The family tree explodes: `PhoneWithCameraAndGps`, and so on.

Composition way: a phone has a `Camera`, a `Gps`, and a `FingerprintReader`. You plug the parts you need into one phone.

## Why this matters in LLD

Inheritance couples the child to the parent's whole shape. A change in `Vehicle` can break every child. Composition lets you swap or reuse one part.

Inheritance is still correct for real is-a models: `Bike : Vehicle`, `King : Piece`. The rule is not "never inherit." The rule is "do not inherit only to reuse a method."

## Bad code — inheritance for reuse

```csharp
public class Bird
{
    public virtual void Fly() { }
    public virtual void Eat() { }
}

public class Penguin : Bird
{
    public override void Fly()
    {
        throw new InvalidOperationException("Cannot fly.");
    }
}
```

You inherited to reuse `Eat`, and you broke the fly promise. That is both an inheritance mistake and an LSP violation.

## Improved code — compose the varying part

```csharp
public interface IFlyBehavior
{
    void Fly();
}

public class CanFly : IFlyBehavior
{
    public void Fly() { }
}

public class CannotFly : IFlyBehavior
{
    public void Fly() { }
}

public class Bird
{
    private readonly IFlyBehavior _fly;

    public Bird(IFlyBehavior fly)
    {
        _fly = fly;
    }

    public void Fly() => _fly.Fly();
    public void Eat() { }
}
```

```text
Bird <>---- IFlyBehavior
               ^
               |
        CanFly / CannotFly
```

A penguin is still a bird. Flying is a has-a behavior, not a forced inherited method that throws.

## Another LLD example — pricing

Bad:

```csharp
public class OrderWithVipPricing : Order
{
    // overrides price calculation
}
```

Better:

```csharp
public class Order
{
    private readonly IPricingStrategy _pricing;

    public Order(IPricingStrategy pricing)
    {
        _pricing = pricing;
    }

    public Money Price() => _pricing.Calculate(this);
}
```

VIP pricing is a strategy the order has, not a new kind of order in the inheritance tree.

## When inheritance is the right call

| Situation | Prefer |
| --- | --- |
| Child is truly a parent for every instance | Inheritance |
| You only want to reuse one method | Composition |
| Behavior may change at runtime | Composition / Strategy |
| Parent has many methods the child cannot honor | Do not inherit. Split the model |
| Shared fields and a custom abstract method | Abstract class inheritance |

```csharp
public abstract class Vehicle
{
    public string PlateNumber { get; }
    protected Vehicle(string plate) => PlateNumber = plate;
    public abstract bool Fits(string spotSize);
}
```

A bike is a vehicle. That inheritance is honest.

## Side by side

```text
Inheritance     Bike --|> Vehicle          bike is a vehicle
Composition     Car  <*>---- Engine        car has an engine
Strategy-style  Order ----> IPricing       order has a pricing rule
```

## Interview answer

> I use inheritance when the is-a sentence is always true. I use composition when I need a part, a swappable behavior, or reuse without taking the whole parent contract.

## Common mistake

Building deep trees because "OOP means inheritance." In interviews, shallow inheritance plus clear composition reads as stronger design.
