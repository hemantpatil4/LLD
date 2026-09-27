# Immutability and value objects

## Immutability

### Simple explanation

An **immutable** object does not change after it is created. To "change" it, you create a new object with the new values.

C# tools for this:

- `readonly` field: the field can be assigned in the constructor, and not later
- `init`: a property can be set while the object is being created, and not later
- `record`: a compact immutable type with value-based equality

### Analogy

A trade confirmation is printed. You do not scratch out the price. You issue a new confirmation.

### Why we need it in LLD

Shared changeable objects cause bugs. Two parts of the design hold the same `Money` and one of them edits the amount. Immutable objects can be passed around freely. They also remove a whole class of threading bugs, because nobody is writing.

### Small C# example

```csharp
public sealed class Money
{
    public decimal Amount { get; }
    public string Currency { get; }

    public Money(decimal amount, string currency)
    {
        Amount = amount;
        Currency = currency;
    }

    public Money Add(Money other)
    {
        if (Currency != other.Currency)
            throw new InvalidOperationException("Currency mismatch.");

        return new Money(Amount + other.Amount, Currency);
    }
}
```

Important lines:

- `Amount` has no `set`. Callers cannot write `money.Amount = 0`.
- `Add` returns a new `Money`. The original stays as it was.
- `sealed` means nobody inherits and quietly adds a setter. It is optional in interviews. It signals "this type is finished."

The same idea as a record:

```csharp
public record Money(decimal Amount, string Currency);
```

A record gives you constructor, equality, and immutability in one line. In an interview, the longer class is fine if you want the rules, such as "no negative amount," written in the constructor.

### Where it appears

Money, address, coordinate, currency pair, date range, a pricing quote that must not be edited after it is offered.

## Value object

### Simple explanation

A **value object** is defined by its values. Two value objects with the same values are interchangeable. They do not have a life story or an id.

An **entity** is defined by identity. Two customers named "Alex" are still two customers, because each has an id.

### Analogy

Two 10-rupee notes of the same condition are interchangeable as value. Two passports with the same name are not. The passport number is the identity.

### Why we need it in LLD

If `CurrencyPair` is a string sprinkled through the program, every class re-checks the format. A value object holds the format rule once. Equality becomes "same pair," which is what pricing code means.

### Small C# example

```csharp
public sealed class CurrencyPair
{
    public string Base { get; }
    public string Quote { get; }

    public CurrencyPair(string baseCurrency, string quoteCurrency)
    {
        Base = baseCurrency;
        Quote = quoteCurrency;
    }

    public override bool Equals(object? obj)
    {
        return obj is CurrencyPair other
            && Base == other.Base
            && Quote == other.Quote;
    }

    public override int GetHashCode() => HashCode.Combine(Base, Quote);
}
```

`new CurrencyPair("USD", "INR")` equals another object built with those same strings. There is no pair id.

A record does this equality for you:

```csharp
public record CurrencyPair(string Base, string Quote);
```

### Where it appears

`Money`, `Address`, `Coordinate`, `CurrencyPair`, `DateRange`. Spot id, ticket id, and customer id mark entities, not value objects.

### A rule you can say in the interview

If I care which one it is, it is an entity. If I only care what it contains, it is a value object.
