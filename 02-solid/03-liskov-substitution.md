# L — Liskov Substitution

## Simple explanation

If a method accepts a parent type, any child object must work in that method without surprises.

The child may add behavior. It must keep the parent's promises: what inputs are allowed, what the method guarantees afterward, and which failures are part of the contract.

## Analogy

You rent a car. The contract says it drives and brakes. A vehicle with four wheels that explodes when you press the brake breaks the contract. Calling it a car does not fix that.

## Why it exists in LLD

Polymorphism only works when the caller can ignore the concrete class. The moment the caller writes `if (bird is Penguin)`, the parent type lied. The design looks extensible and still needs a type check.

## Bad code

```csharp
public class Rectangle
{
    public virtual int Width { get; set; }
    public virtual int Height { get; set; }
    public int Area() => Width * Height;
}

public class Square : Rectangle
{
    public override int Width
    {
        get => base.Width;
        set { base.Width = value; base.Height = value; }
    }

    public override int Height
    {
        get => base.Height;
        set { base.Width = value; base.Height = value; }
    }
}

public static void Stretch(Rectangle shape)
{
    shape.Width = 4;
    shape.Height = 5;
    // Caller expects area 20. A Square produces 25.
}
```

## Why it is bad

`Stretch` is correct for every honest rectangle. Passing a `Square` silently changes the promise: setting the width also sets the height. The child strengthened the rules. The caller cannot trust the parent type.

A second classic failure:

```csharp
public class Bird
{
    public virtual void Fly() { }
}

public class Penguin : Bird
{
    public override void Fly()
    {
        throw new InvalidOperationException("Penguins do not fly.");
    }
}
```

Any method that receives a `Bird` and calls `Fly` crashes on a penguin. The parent promised that birds fly.

## Improved code

Model the real promise, not a convenient family tree.

```csharp
public interface IShape
{
    int Area();
}

public class Rectangle : IShape
{
    public int Width { get; }
    public int Height { get; }

    public Rectangle(int width, int height)
    {
        Width = width;
        Height = height;
    }

    public int Area() => Width * Height;
}

public class Square : IShape
{
    public int Side { get; }

    public Square(int side) => Side = side;

    public int Area() => Side * Side;
}
```

`Rectangle` and `Square` are both shapes. A square is not a rectangle whose width and height are independently assignable. Making both immutable removes the surprise setter.

For birds, put `Fly` only on birds that fly:

```csharp
public interface IBird
{
    void Walk();
}

public interface IFlyingBird : IBird
{
    void Fly();
}
```

A penguin implements `IBird`. An eagle implements `IFlyingBird`. Code that calls `Fly` asks for `IFlyingBird`, so a penguin cannot be passed in.

## LLD example

A parking spot hierarchy. Suppose `ParkingSpot.Park(Vehicle vehicle)` promises that any vehicle which chose this spot will be stored. A `CompactSpot` that throws for a truck breaks callers who were told every `ParkingSpot` can park the vehicle they were given.

Fix the promise:

```csharp
public abstract class ParkingSpot
{
    public abstract bool CanFit(Vehicle vehicle);

    public void Park(Vehicle vehicle)
    {
        if (!CanFit(vehicle))
            throw new InvalidOperationException("Vehicle does not fit.");

        // store the vehicle
    }
}
```

Every child may answer `CanFit` differently. Every child must honor `Park`: it stores the vehicle when `CanFit` is true, and it refuses the same way when `CanFit` is false. Callers use `CanFit` first. They do not special-case compact spots.

## How to spot an LSP violation while designing

Read the parent method as a contract. Then read the child.

A violation is likely when the child:

- throws for an input the parent accepts
- does nothing in an override (`override void Pay` with an empty body)
- demands a stricter input (parent accepts any positive amount, child requires amount > 1000)
- delivers a weaker result (parent says the balance drops by the amount, child sometimes leaves it unchanged)
- forces the caller to write `if (x is SpecialChild)`

The child can do more. It cannot do less than the parent promised.

## Interview question

"If I hold this object as the parent type, which of your methods can surprise me?"

## Common mistake

Treating LSP as "the child inherits the methods." That is just inheritance. LSP is about behavior. A child that compiles and still breaks callers is an LSP violation.
