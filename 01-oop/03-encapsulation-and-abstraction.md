# Encapsulation and abstraction

These two words are neighbors. Interviews expect you to tell them apart.

## Encapsulation

### Simple explanation

**Encapsulation** means the data stays inside the object, and outside code changes that data only by calling methods. Those methods enforce the rules.

### Analogy

An ATM lets you press Withdraw. It does not hand you the cash drawer and a marker so you can write a new balance on the box.

### Why we need it in LLD

Objects in an interview represent real rules. A spot is either free or taken. A balance cannot become whatever a caller types. If fields are public, any class can break those rules, and the bug will not sit in one place.

### Small C# example

```csharp
public class BankAccount
{
    public string AccountId { get; }
    public decimal Balance { get; private set; }

    public BankAccount(string accountId, decimal openingBalance)
    {
        AccountId = accountId;
        Balance = openingBalance;
    }

    public void Withdraw(decimal amount)
    {
        if (amount <= 0)
            throw new InvalidOperationException("Amount must be positive.");

        if (amount > Balance)
            throw new InvalidOperationException("Insufficient balance.");

        Balance -= amount;
    }
}
```

Important lines:

- `private set` means other classes can read `Balance` and cannot assign it.
- `Withdraw` is the only door. The rule lives next to the data.
- A caller who writes `account.Balance = -50000` does not compile.

### Where it appears

`ParkingSpot.Park(vehicle)` is allowed. `spot.IsOccupied = true` from a random class is a broken design, because the spot might be marked occupied with no vehicle stored.

## Abstraction

### Simple explanation

**Abstraction** means the caller sees a simple action and does not see the steps inside.

Encapsulation protects the data. Abstraction simplifies the job the caller must understand.

### Analogy

You press Brew on a coffee machine. You do not open the boiler, start the pump, and seat the valve yourself.

### Why we need it in LLD

A parking exit should say `payment.Pay(fee)`. The story of card networks, receipts, and retries stays inside the payment class. The exit class stays about exits.

### Small C# example

```csharp
public class ExitGate
{
    public void LetOut(Ticket ticket, IPaymentMethod payment)
    {
        decimal fee = ticket.CalculateFee();
        payment.Pay(fee);
        ticket.Close();
    }
}
```

`ExitGate` knows the sequence a driver cares about: fee, pay, close. It does not know how a card is charged.

### Where it appears

`ParkingLot.Park(vehicle)` hides floor search and spot selection. `OrderService.Place(order)` hides validation, pricing, and saving. The name of the method is the abstraction. The private methods are the hidden steps.

## Side by side

```text
Encapsulation: outsiders cannot set Balance directly
Abstraction:   outsiders call Pay and skip the internal steps
```

A good LLD class usually does both. The public methods are the simple story. The private fields are the protected state.
