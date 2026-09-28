# Level 2 practice

Answer these after reading the level. Solutions are not in this file. Level 1 practice is still open.

## 1. Single Responsibility

`ParkingLot` can park a car, calculate a fee, send an SMS, and insert a row in SQL. Split it.

Name each type and its one job. Say which type is only a coordinator.

## 2. Open/Closed

A fee method branches on bike, car, and truck. Show an `IFeeRule` version.

Then answer: a new electric-vehicle fee arrives tomorrow. Which type do you add, and which method do you leave unchanged?

## 3. Liskov Substitution

`Penguin : Bird` overrides `Fly` and throws. Explain the broken promise.

Show a better pair of types so that code which calls `Fly` cannot receive a penguin.

## 4. Interface Segregation

`IMachine` has `Park`, `TakePayment`, `SendEmail`, and `GenerateDailyReport`. An entry gate can only park.

Which interfaces would you create, and which ones does the entry gate implement?

## 5. Dependency Inversion

`OrderService.Place` contains `new SmtpEmailSender()`.

Draw the arrow before the change and after the change. Show the constructor you would write.

## 6. Spot the violations

```csharp
public class TradeBookingService
{
    public void Book(string pair, decimal amount, string notifyBy)
    {
        if (notifyBy == "email")
            new SmtpEmailSender().Send("Booked");
        else if (notifyBy == "sms")
            new TwilioSmsSender().Send("Booked");

        new SqlTradeRepository().Save(pair, amount);
    }
}
```

Name the principles this breaks. Skip any principle that is not really present.

## 7. Interview question

Parking exit must accept card now and UPI later. The gate also computes a simple hourly fee that is the same for every vehicle.

Which SOLID principles are you applying, and which ones are you deliberately not applying yet? A short design is enough: the types, who calls whom, and where `new` happens.
