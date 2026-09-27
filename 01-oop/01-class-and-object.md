# Level 1 — Class and object

First lesson only. The exercise at the bottom has no solution yet.

## 1. Simple explanation

A **class** is a blueprint. It describes what data a thing has and what actions that thing can perform.

An **object** is one real thing built from that blueprint.

```text
Class   = the form you fill for a customer
Object  = one filled form for one customer
```

In C#:

```csharp
public class Customer   // blueprint
{
    public string Name { get; set; }
}

Customer ria = new Customer();   // one real customer
```

`Customer` is the class. `ria` is the object.

## 2. Real-world analogy

A parking-lot company prints one ticket template.

- The template is the class: it has fields for vehicle number, entry time, and spot.
- Each car that enters gets its own ticket. That ticket is an object.
- Two cars do not share one ticket. Each object has its own values.

## 3. Why this exists in LLD

An interview problem is a pile of nouns: car, ticket, spot, payment.

If you only write loose variables, you cannot say "this ticket belongs to this car" or "this spot is free."

A class groups:

- the data that belongs together
- the actions that are allowed on that data

An object is one live instance during the scenario: this car, this ticket, this spot.

Without classes, the design is a script. With classes, the design is a model of the real world.

## 4. Very small C# example

An FX order. One class. Two objects. Each object keeps its own currency pair and amount.

```csharp
public class FxOrder
{
    public string CurrencyPair { get; set; }
    public decimal Amount { get; set; }

    public string Describe()
    {
        return CurrencyPair + " " + Amount;
    }
}

FxOrder first = new FxOrder();
first.CurrencyPair = "USDINR";
first.Amount = 100000m;

FxOrder second = new FxOrder();
second.CurrencyPair = "EURUSD";
second.Amount = 250000m;
```

Important lines:

- `public class FxOrder` defines the blueprint. It is not an order yet.
- `new FxOrder()` creates one object in memory.
- `first` and `second` are two objects. Changing `first.Amount` does not change `second.Amount`.
- `Describe()` is behavior that belongs to an order, so it lives on the class.

## 5. Where this appears in an LLD problem

Problem: "Design a parking lot."

| Noun in the problem | Class or object? | Why |
| --- | --- | --- |
| The idea of a vehicle | Class `Vehicle` | Every vehicle has a number and a type |
| The Honda that just entered | Object | One specific vehicle |
| The idea of a ticket | Class `Ticket` | Every ticket has an entry time and a spot |
| Ticket number 1042 | Object | One specific visit |

You do not create a class for "the Honda." You create a class `Vehicle`, then one object for that Honda.

## 6. What is not a class yet

Not every word becomes a class.

- `entryTime` is a value on `Ticket`, not its own class, until time has rules of its own.
- `isOccupied` is a true/false value on a spot, not a class.
- "park the car" is a method, not a class.

Rule for now: a class represents a thing that has its own data and its own actions. A single number, flag, or string usually stays as a field.

## Exercise

Do this before the next lesson. Write the answer in your reply. No need for a full program.

**Small exercise**

A vending machine sells items. Each item has a name, a price, and a stock count.

1. What is the class?
2. What are three objects you would create for a demo?
3. Which of these should **not** be a class, and why: `Item`, `price`, `buy`?

**Interview-style problem**

> Design the objects for a library desk. A member borrows a book and receives a loan record. The library tracks which copy was borrowed.

List only:

- the classes you would create
- two objects for one real borrow
- one word from the sentence that you would keep as a field or a method, not as a class

Stop there. I will review it, then we move to the next OOP idea.
