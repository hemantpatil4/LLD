# O — Open/Closed

## Simple explanation

Add a new behavior by adding a new class. Leave the working classes alone.

Open for extension: a new vehicle type, a new payment method, a new pricing rule. Closed for modification: the parking workflow is not reopened for each addition.

## Analogy

A power strip already works. You plug in a new charger. You do not rewire the strip.

## Why it exists in LLD

The interview follow-up is almost always "what if tomorrow we add X?" If the answer is "I add another `else if` in the middle of the lot," every new product edits a class that already works. That edit can break cars while you are adding buses.

## Bad code

```csharp
public decimal CalculateFee(string vehicleType, int hours)
{
    if (vehicleType == "bike")
        return hours * 10m;
    if (vehicleType == "car")
        return hours * 20m;
    if (vehicleType == "truck")
        return hours * 40m;

    throw new InvalidOperationException("Unknown vehicle");
}
```

## Why it is bad

A bicycle-with-trailer, or an electric car, edits this method. The car branch can be broken by a careless edit meant only for the new type. The compiler does not tell you that you forgot a branch.

## Improved code

```csharp
public interface IFeeRule
{
    string VehicleType { get; }
    decimal Calculate(int hours);
}

public class CarFeeRule : IFeeRule
{
    public string VehicleType => "car";
    public decimal Calculate(int hours) => hours * 20m;
}

public class FeeCalculator
{
    private readonly List<IFeeRule> _rules;

    public FeeCalculator(List<IFeeRule> rules)
    {
        _rules = rules;
    }

    public decimal Calculate(string vehicleType, int hours)
    {
        foreach (IFeeRule rule in _rules)
        {
            if (rule.VehicleType == vehicleType)
                return rule.Calculate(hours);
        }

        throw new InvalidOperationException("Unknown vehicle");
    }
}
```

A new type is a new class: `BusFeeRule : IFeeRule`. `FeeCalculator.Calculate` stays as it is. You register the new rule wherever objects are created.

## LLD example

Parking fees, payment methods, notification channels, pricing rules on an FX desk, chess piece moves. The stable part is the workflow. The varying part is the rule.

## How to spot an OCP violation while designing

Look for a switch or an `if` / `else` on a type name or a string code such as `"CARD"` or `"UPI"`.

Ask: "If we add one more kind, which file changes?"

- If a new file appears and the workflow file does not, the design is open.
- If `ParkingService` or `CalculateFee` must be edited, it is closed only in theory.

Leave the switch in place when the set of cases is finished and small: a coin's heads and tails, or a result of success and failure. One `if` for a single known case is clearer than a strategy interface with one implementation.

## Interview question

"What if we add electric vehicle charging as a new fee?"

Show the new class. Point at the old method and say it stays unchanged.

## Common mistake

Inventing `IFeeRule` when the product has one formula and nobody expects a second. OCP serves variation you can name. It is not a requirement to hide every `if`.
