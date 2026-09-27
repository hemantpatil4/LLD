# Assessment 1 of 10 — Encapsulation

No solution in this file until you attempt the question.

## Question

You are designing a `BankAccount` used during FX settlement. A teammate writes:

```csharp
public class BankAccount
{
    public decimal Balance;
}
```

A caller then does:

```csharp
account.Balance = -50000;
```

Answer in your own words:

1. What is wrong with this design?
2. How would you change the class so outside code cannot put the account into an invalid state?
3. Why does this matter in an LLD interview, beyond coding style?
