# Entity, service, and DTO

Three kinds of types show up in almost every LLD. Mixing them up produces a class that both stores the business thing and knows the database, the API, and every workflow.

## Entity

### Simple explanation

An **entity** is a business thing with an identity. It lives over time. Its fields can change, and it is still the same thing because the id stayed.

Examples: `Customer`, `Order`, `Vehicle`, `Ticket`, `ParkingSpot`.

### Analogy

Your account number stays the same while the balance moves. The account is the entity. The balance is state on that entity.

### Why we need it in LLD

The nouns in the problem that you track, look up, and update are entities. They own the rules about their own state. A ticket should know whether it is open. A spot should know whether it can accept a vehicle.

### Small C# example

```csharp
public class Ticket
{
    public string Id { get; }
    public DateTime EntryTime { get; }
    public DateTime? ExitTime { get; private set; }

    public Ticket(string id, DateTime entryTime)
    {
        Id = id;
        EntryTime = entryTime;
    }

    public void Close(DateTime exitTime)
    {
        ExitTime = exitTime;
    }
}
```

Same ticket id, new exit time. That is an entity changing state.

### Where it appears

The boxes in the middle of your class diagram.

## Service

### Simple explanation

A **service** does a job that spans objects or does not naturally belong to one entity. It usually has little or no identity of its own. You would not store "pricing service number 4" as a business record.

Examples: `PaymentService`, `PricingService`, `NotificationService`, `ParkingService`.

### Analogy

The teller is not the account. The teller carries out a withdrawal by talking to the account, the cash drawer, and the receipt printer.

### Why we need it in LLD

Some work touches several entities. Parking a car updates a spot and creates a ticket. If you put that whole story on `Vehicle`, the vehicle starts running the lot. If you put it all on `ParkingLot`, the lot becomes a god class: one class that knows every rule.

A service coordinates. Entities keep their own small rules.

Put a rule on the entity when the sentence is "this thing refuses to be invalid." Put a rule on the service when the sentence is "this workflow uses several things."

### Small C# example

```csharp
public class PricingService
{
    public Money Quote(CurrencyPair pair, decimal amount, decimal rate)
    {
        return new Money(amount * rate, pair.Quote);
    }
}
```

`PricingService` does not have an id. `CurrencyPair` and `Money` are values it uses. An `Order` entity would store the quote it was given.

### Where it appears

The verbs that are bigger than one noun: park, checkout, settle, notify, match a ride.

## DTO

### Simple explanation

A **DTO** (data transfer object) carries data across a boundary. It is a shape for input or output. It does not enforce business rules.

**Domain object** means the entity or value object inside the model, the one that does enforce rules.

Examples of DTOs: `CreateOrderRequest`, `TicketResponse`, a message you put on Kafka.

### Analogy

The paper form a customer fills at the counter is not the account. A clerk reads the form and then opens a real account. The form can be incomplete. The account object should not be.

### Why we need it in LLD

API fields change because a screen changed. Domain rules change because the business changed. If one class plays both roles, a screen change can punch a hole in the rule, or a rule can force every caller to send fields they do not have.

In many LLD interviews you never need a DTO, because there is no API boundary. Introduce one when you say "this is the request coming in from outside."

### Small C# example

```csharp
public class CreateOrderRequest
{
    public string CurrencyPair { get; set; } = "";
    public decimal Amount { get; set; }
}

public class Order
{
    public string Id { get; }
    public CurrencyPair Pair { get; }
    public Money Amount { get; }

    public Order(string id, CurrencyPair pair, Money amount)
    {
        if (amount.Amount <= 0)
            throw new InvalidOperationException("Amount must be positive.");

        Id = id;
        Pair = pair;
        Amount = amount;
    }
}
```

`CreateOrderRequest` is allowed to arrive empty. The code that handles it must check the fields, then build an `Order`. Once `Order` exists, the amount is positive. That check lives on the domain object.

### Where it appears

Controller input, Kafka payloads, repository rows if you want to keep SQL types away from the domain. The interview-friendly sentence is: "The request is a DTO. The order is the domain object. A mapper or the service builds one from the other."

## How to sort a noun

| Question | If yes |
| --- | --- |
| Do I look this up by id and update it later? | Entity |
| Is it fully described by its values, with no id? | Value object |
| Is it a job that uses several objects? | Service |
| Is it only the shape of data crossing a boundary? | DTO |
